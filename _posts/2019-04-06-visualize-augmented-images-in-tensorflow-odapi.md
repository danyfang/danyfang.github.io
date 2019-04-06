---
layout: post
title:  "Visualize Augmented Images in Tensorflow ODAPI"
categories: tensorflow object-detection
tags: tensorflow object-detection-api data-augmentation
author: xui
---

* content
{:toc}

# Visualize augmented images in tensorflow object detection api
## Introduction
Google released its tensorflow object detectio api to open source and this api is really well-provided, both in terms of the framework design and source code quality. I have been reading the source code a few months ago and I am now able to utilize the api to provide some custom functionalities.

We all know that having enough training data in deep learning is important but sometimes it is very difficult to gather as much data as you need. And data augmentation is an useful technique to make some transformations to the original data so that there are more data to feed to the model during training.


I came across several questions related to the augmentation options within the api. The augmentations are part of the whole training pipeline, so it is not easy to visualize the augmented images, and some users find it very difficult to understand what kind of augmentation options they need.


I read the source code and I found that the testing package provided the tool for us to test the augmentation options, I made a little change and wrote the following script `augmentation_test.py`. 


## Augmentation\_test.py
```python 
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function

import functools
import os
from absl.testing import parameterized

import numpy as np
import tensorflow as tf
from scipy.misc import imsave, imread

from object_detection import inputs
from object_detection.core import preprocessor
from object_detection.core import standard_fields as fields
from object_detection.utils import config_util
from object_detection.utils import test_case

FLAGS = tf.flags.FLAGS

class DataAugmentationFnTest(test_case.TestCase):

  def test_apply_image_and_box_augmentation(self):
    data_augmentation_options = [
        (preprocessor.random_horizontal_flip, {
        })
    ]
    data_augmentation_fn = functools.partial(
        inputs.augment_input_data,
        data_augmentation_options=data_augmentation_options)
    tensor_dict = {
        fields.InputDataFields.image:
            tf.constant(imread('input.image').astype(np.float32)),
        fields.InputDataFields.groundtruth_boxes:
            tf.constant(np.array([[.5, .5, 1., 1.]], np.float32))
    }
    augmented_tensor_dict = 
        data_augmentation_fn(tensor_dict=tensor_dict)
    with self.test_session() as sess:
      augmented_tensor_dict_out = sess.run(augmented_tensor_dict)
    imsave('output.image',augmented_tensor_dict_out[fields.InputDataFields.image])


if __name__ == '__main__':
  tf.test.main()
```

The code above took some code samples from the testing package [input\_test.py](https://github.com/tensorflow/models/blob/master/research/object_detection/inputs_test.py). In the script, it defined a `DataAugmentationFnTest` test class and this class has a method called `test_apply_image_and_box_augmentation`.

## Usage
There are several parts to be configured in order to visualize the augmentation effects on the images.
`input.image` and `output.image` need to be configured to the correct input image path and output image path. 
`data_augmentation_options = []`, in this list, you should add the augmentation options that you want to test, if you have several options, you should append them to the list as well.



## Result
I tested with the lena image and here is the result after `random\_horizontal\_flip`.
![lena](../../../../assets/lena.png)
![lena_out](../../../../assets/lena_out.png)

