---
title: Video Codec Testing and Quality Measurement
docname: draft-daede-netvc-testing-latest
date: 2015-03-09
category: info

ipr: trust200902
area: RAI
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: T. Daede
    name: Thomas Daede
    org: Mozilla
    email: tdaede@mozilla.com
 -
    ins: J. Moffitt
    name: Jack Moffitt
    org: Mozilla
    email: jack@metajack.im

normative:

informative:
  PSNRHVS:
    title: A New Full-Reference Quality Metrics Based on HVS
    author:
      -
        ins: K. Egiazarian
        name: Karen Egiazarian
      -
        ins: J. Astola
        name: Jaakko Astola
      -
        ins: N. Ponomarenko
        name: Nikolay Ponomarenko
      -
        ins: V. Lukin
        name: Vladimir Lukin
      -
        ins: F. Battisti
        name: Federica Battisti
      -
        ins: M. Carli
        name: Marco Carli
    date: 2002
  FASTSSIM:
    target: http://live.ece.utexas.edu/publications/2011/chen_rtip_2011.pdf
    title: Fast structural similarity index algorithm
    author:
      -
        ins: M. Chen
        name: Ming-Jun Chen
      -
        ins: A. C. Bovik
        name: Alan Conrad Bovik
    date: 2010
  MSSSIM:
    target: http://www.cns.nyu.edu/~zwang/files/papers/msssim.pdf
    title: Multi-Scale Structural Similarity for Image Quality Assessment
    author:
      -
        ins: Z. Wang
        name: Zhou Wang
      -
        ins: E. P. Simoncelli
        name: Eero P. Simoncelli
      -
        ins: A. C. Bovik
        name: Alan Conrad Bovik
  SSIM:
    target: http://www.cns.nyu.edu/pub/eero/wang03-reprint.pdf
    title: "Image Quality Assessment: From Error Visibility to Structural Similarity"
    author:
      -
        ins: Z. Wang
        name: Zhou Wang
      -
        ins: A. C. Bovik
        name: Alan Conrad Bovik
      -
        ins: H. R. Sheikh
        name: Hamid Rahim Sheikh
      -
        ins: E. P. Simoncelli
        name: Eero P. Simoncelli
    date: 2004
  DAALA-GIT:
    target: http://git.xiph.org/?p=daala.git;a=summary
    title: Daala Git Repository
    author:
      org: Xiph.Org
    date: 2015
  AWCY:
    target: https://arewecompressedyet.com/
    author:
      org: Xiph.Org
    title: Are We Compressed Yet?
    date: 2015
  L1100:
    target: http://phenix.int-evry.fr/jct/
    title: Common test conditions and software reference configurations
    author:
      -
        ins: F. Bossen
        name: Frank Bossen
    date: 2013
    seriesinfo:
      JCTVC: L1100
  COMPARECODECS:
    target: http://compare-codecs.appspot.com/
    title: Compare Codecs
    author:
      -
        ins: H. Alvestrand
        name: Harald Alvestrand
    date: 2015

--- abstract

This document describes guidelines and procedures for evaluating an internet video codec specified at the IETF. This covers subjective and objective tests, test conditions, and materials used for the test.

--- middle

# Introduction

When developing an internet video codec, changes and additions to the codec need to be decided based on their performance tradeoffs. In addition, measurements are needed to determine when the codec has met its performance goals. This document specifies how the tests are to be carried about to ensure valid comparisons and good decisions.

# Subjective Metrics

Subjective testing is the preferable method of testing video codecs.

Because the IETF does not have testing resources of its own, it has to rely on the resources of its participants. For this reason, even if the group agrees that a particular test is important, if no one volunteers to do it, or if volunteers do not complete it in a timely fashion, then that test should be discarded.  This ensures that only important tests be done in particular, the tests that are important to participants.

# Objective Metrics

Objective metrics are used in place of subjective metrics for easy and repeatable experiments. Most objective metrics have been designed to correlate with subjective scores.

The following descriptions give an overview of the operation of each of the metrics. Because implementation details can sometimes vary, the exact implementation is specified in C in the Daala tools repository {{DAALA-GIT}}.

All of the metrics described in this document are to be applied to the luma plane only. In addition, they are single frame metrics. When applied to the video, the scores of each frame are averaged to create the final score.

Codecs are allowed to internally use downsampling, but must include a normative upsampler, so that the metrics run at the same resolution as the source video. In addition, some metrics, such as PSNR and FASTSSIM, have poor behavior on downsampled images, so it must be noted in test results if downsampling is in effect.

## PSNR

PSNR is a traditional signal quality metric, measured in decibels. It is directly drived from mean square error (MSE), or its square root (RMSE). The formula used is:

20 * log10 ( MAX / RMSE )

or, equivalently:

10 * log10 ( MAX^2 / MSE )

which is the method used in the dump_psnr.c reference implementation.

## PSNR-HVS-M

The PSNR-HVS metric performs a DCT transform of 8x8 blocks of the image, weights the coefficients, and then calculates the PSNR of those coefficients. Several different sets of weights have been considered. {{PSNRHVS}} The weights used by the dump_pnsrhvs.c tool in the Daala repository have been found to be the best match to real MOS scores.

## SSIM

SSIM (Structural Similarity Image Metric) is a still image quality metric introduced in 2004 {{SSIM}}. It computes a score for each individual pixel, using a window of neighboring pixels. These scores can then be averaged to produce a global score for the entire image. The original paper produces scores ranging between 0 and 1.

For the metric to appear more linear on BD-rate curves, the score is converted into a nonlinear decibel scale:

-10 * log10 (1 - SSIM)

## Fast Multi-Scale SSIM

Multi-Scale SSIM is SSIM extended to multiple window sizes {{MSSSIM}}. This is implemented in the Fast implementation by downscaling the image a number of times, and computing SSIM over the same number of pixels, then averaging the SSIM scores together {{FASTSSIM}}. The final score is converted to decibels in the same manner as SSIM.

# Comparing and Interpreting Results

## Graphing

When displayed on a graph, bitrate is shown on the X axis, and the quality metric is on the Y axis. For clarity, the X axis bitrate is always graphed in the log domain. The Y axis metric should also be chosen so that the graph is approximately linear. For metrics such as PSNR and PSNR-HVS, the metric result is already in the log domain and is left as-is. SSIM and FASTSSIM, on the other hand, return a result between 0 and 1. To create more linear graphs, this result is converted to a value in decibels:

-1 * log10 ( 1 - SSIM )

## Bjontegaard

The Bjontegaard rate difference, also known as BD-rate, allows the comparison of two different codecs based on a metric. This is commonly done by fitting a curve to each set of data points on the plot of bitrate versus metric score, and then computing the difference in area between each of the curves. A cubic polynomial fit is common, but will be overconstrained with more than four samples. For higher accuracy, at least 10 samples and a linear piecewise fit should be used.

# Test Sequences

## Sources

Lossless test clips are preferred for most tests, because the structure of compression artifacts in already-compressed clips may introduce extra noise in the test results. However, a large amount of content on the internet needs to be recompressed at least once, so some sources of this nature are useful. The encoder should run at the same bit depth as the original source. In addition, metrics need to support operation at high bit depth. If one or more codecs in a comparison do not support high bit depth, sources need to be converted once before entering the encoder.

The JCT-VC standards organization includes a set of standard test clips for video codec testing, and parameters to run the clips with {{L1100}}. These clips are not publicly available, but are very useful for comparing to published results.

Xiph publishes a variety of test clips collected from various sources.

The Blender Open Movie projects provide a large test base of lossless cinematic test material. The lossless sources are available, hosted on Xiph.

## Usage Scenarios

- Streaming video consists of cinematic content, with a minimum source resolution of 1920x1080 at 24 to 30 frames per second. Example test clips that fit into this category:
  - Sintel
  - Tears of Steel
  - Kimono1
  - Tennis
  - PeopleOnStreet

- Videoconferencing content is high framerate, and varying HD resolutions. Examples:
  - KristenAndSara
  - FourPeople
  - Johnny

- Screensharing content is low framerate, high resolution content typical of a computer desktop.
  - SlideEditing
  - SlideShow

- Game streaming content is synthetically generated content, with varying resolutions but typically recorded at 60 frames per second.
  - ChinaSpeed
  - Touhou

# Automation

Frequent objective comparisons are extremely beneficial while developing a new codec. Several tools exist in order to automate the process of objective comparisons. The Compare-Codecs tool allows BD-rate curves to be generated for a wide variety of codecs {{COMPARECODECS}}. The Daala source repository contains a set of scripts that can be used to automate the various metrics used. In addition, these scripts can be run automatically utilizing distributed computer for fast results {{AWCY}}.

