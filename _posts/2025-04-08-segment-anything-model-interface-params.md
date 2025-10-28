---
title: Segment Anything Model (SAM) 的接口参数
date: 2025-04-08 12:00:00
category: 技术
tags: [AI,Python]
---

Segment Anything Model (SAM) 是一个分割模型。它的接口参数只有很简单的文档说明。
我在使用中弄清楚了其含义，本文做个解释。如无特殊说明，这里提到的SAM是指其当前最新版本[SAM 2](https://ai.meta.com/sam2/)。

<!--more-->

一般来说，SAM接受用户指定的点或框来作为提示（prompts），然后推理给出想要的物体掩膜。
对于图片分割，直接使用[`SAM2ImagePredictor.predict`](https://github.com/facebookresearch/sam2/blob/2b90b9f5ceec907a1c18123530e92e794ad901a4/sam2/sam2_image_predictor.py#L237)即可。
该函数输入为提示（除点和框外，还接受掩膜作为提示），输出为目标物体的掩膜。
对于视频的分割和追踪，实现同样功能的是`SAM2VideoPredictor`的[`add_new_points_or_box`](https://github.com/facebookresearch/sam2/blob/2b90b9f5ceec907a1c18123530e92e794ad901a4/sam2/sam2_video_predictor.py#L161)和[`add_new_mask`](https://github.com/facebookresearch/sam2/blob/2b90b9f5ceec907a1c18123530e92e794ad901a4/sam2/sam2_video_predictor.py#L300)函数。
这两个函数对视频的某一帧添加提示、进行分割并返回结果，同时也为追踪提供了物体信息。
在对某些帧（通常是第一帧）添加过提示之后，就可以调用[`SAM2VideoPredictor.add_new_points_or_box`](https://github.com/facebookresearch/sam2/blob/2b90b9f5ceec907a1c18123530e92e794ad901a4/sam2/sam2_video_predictor.py#L546)来进行追踪，返回整个视频中各帧各物体的时空坐标（SMA 2称为masklet）。

上面这几个功能及其接口都容易理解。我想要解释的其实是另一个功能的接口，即为一张图片自动生成所有物体的掩膜。
这一功能通过下面的类[SAM2AutomaticMaskGenerator](https://github.com/facebookresearch/sam2/blob/2b90b9f5ceec907a1c18123530e92e794ad901a4/sam2/automatic_mask_generator.py#L36)来实现：

```python
class SAM2AutomaticMaskGenerator:
    def __init__(
        self,
        model: SAM2Base,
        points_per_side: Optional[int] = 32,
        points_per_batch: int = 64,
        pred_iou_thresh: float = 0.8,
        stability_score_thresh: float = 0.95,
        stability_score_offset: float = 1.0,
        mask_threshold: float = 0.0,
        box_nms_thresh: float = 0.7,
        crop_n_layers: int = 0,
        crop_nms_thresh: float = 0.7,
        crop_overlap_ratio: float = 512 / 1500,
        crop_n_points_downscale_factor: int = 1,
        point_grids: Optional[List[np.ndarray]] = None,
        min_mask_region_area: int = 0,
        output_mode: str = "binary_mask",
        use_m2m: bool = False,
        multimask_output: bool = True,
        **kwargs,
    ) -> None:
        """
        Using a SAM 2 model, generates masks for the entire image.
        Generates a grid of point prompts over the image, then filters
        low quality and duplicate masks. The default settings are chosen
        for SAM 2 with a HieraL backbone.

        Arguments:
          model (Sam): The SAM 2 model to use for mask prediction.
          points_per_side (int or None): The number of points to be sampled
            along one side of the image. The total number of points is
            points_per_side**2. If None, 'point_grids' must provide explicit
            point sampling.
          points_per_batch (int): Sets the number of points run simultaneously
            by the model. Higher numbers may be faster but use more GPU memory.
          pred_iou_thresh (float): A filtering threshold in [0,1], using the
            model's predicted mask quality.
          stability_score_thresh (float): A filtering threshold in [0,1], using
            the stability of the mask under changes to the cutoff used to binarize
            the model's mask predictions.
          stability_score_offset (float): The amount to shift the cutoff when
            calculated the stability score.
          mask_threshold (float): Threshold for binarizing the mask logits
          box_nms_thresh (float): The box IoU cutoff used by non-maximal
            suppression to filter duplicate masks.
          crop_n_layers (int): If >0, mask prediction will be run again on
            crops of the image. Sets the number of layers to run, where each
            layer has 2**i_layer number of image crops.
          crop_nms_thresh (float): The box IoU cutoff used by non-maximal
            suppression to filter duplicate masks between different crops.
          crop_overlap_ratio (float): Sets the degree to which crops overlap.
            In the first crop layer, crops will overlap by this fraction of
            the image length. Later layers with more crops scale down this overlap.
          crop_n_points_downscale_factor (int): The number of points-per-side
            sampled in layer n is scaled down by crop_n_points_downscale_factor**n.
          point_grids (list(np.ndarray) or None): A list over explicit grids
            of points used for sampling, normalized to [0,1]. The nth grid in the
            list is used in the nth crop layer. Exclusive with points_per_side.
          min_mask_region_area (int): If >0, postprocessing will be applied
            to remove disconnected regions and holes in masks with area smaller
            than min_mask_region_area. Requires opencv.
          output_mode (str): The form masks are returned in. Can be 'binary_mask',
            'uncompressed_rle', or 'coco_rle'. 'coco_rle' requires pycocotools.
            For large resolutions, 'binary_mask' may consume large amounts of
            memory.
          use_m2m (bool): Whether to add a one step refinement using previous mask predictions.
          multimask_output (bool): Whether to output multimask at each point of the grid.
        """
    ...
    ...
```


使用这个类的主要参数就是在初始化时指定的这些，进行推理时反而简单调用`generate(self, image)`即可。
它的工作原理是自动在图片中选出一些点作为提示，然后进行分割；还支持从原始图片中截取出部分小图来多次处理；最后对所有分割结果进行过滤，筛出有价值的掩膜。

这些初始化参数中，有些（`points_per_side`、`point_grids`、`crop_n_points_downscale_factor`、`crop_n_layers`、`crop_overlap_ratio`）用于指定取哪些点作为提示（包括截取的小图中的点），另外大多用于指定对结果过滤筛选的条件，尤其是几个以`thresh`结尾的阈值。
其中`pred_iou_thresh`、`box_nms_thresh`, `crop_nms_thresh`的含义比较易懂，而`stability_score_thresh`则不太好理解。
我是通过阅读[源码](https://github.com/facebookresearch/sam2/blob/main/sam2/utils/amg.py#L158)才弄清楚文档里所说的“the stability of the mask under changes to the cutoff used to binarize the model's mask predictions”是什么意思。
下面展开介绍一下。

先要从`mask_threshold`说起。模型推理出来的是反映每个像素是否属于当前物体的对数几率值（logits），要通过`mask_threshold`将其二值化为0、1排布的掩膜。
`mask_threshold`默认值为0，是因为这些logits以0为中心（值域为[-20, 20]）。
如果增大或减小`mask_threshold`，部分接近0的logits（一般对应物体边界的像素）将产生不同的二值化结果，从而影响得到的掩膜。
所谓稳定，就是即使`mask_threshold`稍有增减，结果掩膜的变化也不大。我们可以通过计算变化前后掩膜区域的交并比来衡量稳定性，这就是稳定性分数。
计算时“稍有增减”的幅度通过`stability_score_offset`参数来指定（默认值为1.0）。

理解含义之后，就能通过调整这些接口参数来改变最终返回的掩膜数量。例如，要返回更多掩膜，可以减小`pred_iou_thresh`、`stability_score_thresh`、`stability_score_offset`，增大`box_nms_thresh`、`crop_nms_thresh`。当然，撒更多的点也行，不过推理计算量也就相应增加了。
