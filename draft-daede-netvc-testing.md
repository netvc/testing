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
    title: A NEW FULL-REFERENCE QUALITY METRICS BASED ON HVS
    author:
      -
        name: Karen Egiazarian
      -
        name: Jaakko Astola
      -
        name: Nikolay Ponomarenko
      -
        name: Vladimir Lukin
      -
        name: Federica Battisti
      -
        name: Marco Carli
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
  MSSSIM:
    target: http://www.cns.nyu.edu/~zwang/files/papers/msssim.pdf
    title: MULTI-SCALE STRUCTURAL SIMILARITY FOR IMAGE QUALITY ASSESSMENT
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
  DAALA-GIT:
    target: http://git.xiph.org/?p=daala.git;a=summary
    title: Daala Git Repository
  AWCY:
    target: https://arewecompressedyet.com/
    title: Are We Compressed Yet?

--- abstract

This document describes a standard procedure for objective and subjective tests to determine the quality versus bitrate tradeoff for a digital video codec.

--- middle

# Introduction

*todo*

# Subjective Metrics

Subjective testing is the preferable method of testing video codecs.

Because the IETF does not have testing resources of its own, it has to rely on the resources of its participants. For this reason, even if the group agrees that a particular test is important, if no one volunteers to do it, or if volunteers do not complete it in a timely fashion, then that test should be discarded.  This ensures that only important tests be done in particular, the tests that are important to participants.

# Objective Metrics

Objective metrics are used in place of subjective metrics for easy and repeatable experiments. Most objective metrics have been designed to correlate with subjective scores.

The following descriptions give an overview of the operation of each of the metrics. Because implementation details can sometimes vary, the exact implementation is specified in C in the Daala tools repository [DAALA-GIT].

All of the metrics described in this document are to be applied to the luma plane only. In addition, they are single frame metrics. When applied to the video, the scores of each frame are averaged to create the final score.

## PSNR

PSNR is a traditional signal quality metric, measured in decibels. It is directly drived from mean square error (MSE), or its square root (RMSE). The formula used is:

20 * log10 ( MAX / RMSE )

or, equivalently:

10 * log10 ( MAX^2 / MSE )

which is the method used in the dump_psnr.c reference implementation.

## PSNR-HVS-M

The PSNR-HVS metric performs a DCT transform of 8x8 blocks of the image, weights the coefficients, and then calculates the PSNR of those coefficients. Several different sets of weights have been considered. The weights used by the dump_pnsrhvs.c tool have been found to be the best match to real MOS scores.

## SSIM

SSIM (Structural Similarity Image Metric) is a still image quality metric introduced in 2004. It computes a score for each individual pixel, using a window of neighboring pixels. These scores can then be averaged to produce a global score for the entire image. The original paper produces scores ranging between 0 and 1.

For the metric to appear more linear on BD-rate curves, the score is converted into a nonlinear decibel scale:

-10 * log10 (1 - SSIM)

## Fast Multi-Scale SSIM

Multi-Scale SSIM is SSIM extended to multiple window sizes. This is implemented by downscaling the image a number of times, and computing SSIM over the same number of pixels, then averaging the SSIM scores together. The final score is converted to decibels in the same manner as SSIM.

# Comparing and Interpreting Results

## Bjontegaard

## Scales

When displayed on a graph, bitrate is shown on the X axis, and the quality metric is on the Y axis. For clarity, the X axis bitrate is always graphed in the log domain. The Y axis metric should also be chosen so that the graph is approximately linear. For metrics such as PSNR and PSNR-HVS, the metric result is already in the log domain and is left as-is. SSIM and FASTSSIM, on the other hand, return a result between 0 and 1. To create more linear graphs, this result is converted to a value in decibels:

-1 * log10 ( 1 - SSIM )

## Averaging results from multiple sequences

# Test Sequences

## Sources

Lossless test clips are preferred for most tests, because the structure of compression artifacts in already-compressed clips may introduce extra noise in the test results. However, a large amount of content on the internet needs to be recompressed at least once, so some sources of this nature are useful.

The JCT-VC standards organization includes a set of standard test clips for video codec testing. These clips are not publicly available, but are very useful for comparing to published results.

Xiph publishes a variety of test clips collected from various sources.

The Blender Open Movie projects provide a large test base of lossless cinematic test material. The lossless sources are available, hosted on Xiph.

## Sets

# Automation

