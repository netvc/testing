---
title: Video Codec Testing and Quality Measurement
docname: draft-ietf-netvc-testing-latest
date: 2019-01-25
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
    ins: A. Norkin
    name: Andrey Norkin
    org: Netflix
    email: anorkin@netflix.com
 -
    ins: I. Brailovskiy
    name: Ilya Brailovskiy
    org: Amazon Lab126
    email: brailovs@lab126.com

normative:

informative:
  I-D.ietf-netvc-requirements:
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
    date: 2016
  RD_TOOL:
    target: https://github.com/tdaede/rd_tool
    author:
      org: Xiph.Org
    title: rd_tool
    date: 2016
  COMPARECODECS:
    target: http://compare-codecs.appspot.com/
    title: Compare Codecs
    author:
      -
        ins: H. Alvestrand
        name: Harald Alvestrand
    date: 2015
  TESTSEQUENCES:
    target: https://people.xiph.org/~tdaede/sets/
    title: Test Sets
    author:
      -
        ins: T. Daede
        name: Thomas Daede
  CIEDE2000:
    target: http://dx.doi.org/10.1155/2012/273723
    title: Color Image Quality Assessment Based on CIEDE2000
    author:
      -
        ins: Y. Yang
        name: Yang Yang
      -
        ins: J. Ming
        name: Jun Ming
      -
        ins: N. Yu
        name: Nenghai Yu
    date: 2012
  VMAF:
    target: "https://github.com/Netflix/vmaf"
    title: VMAF - Video Multi-Method Assessment Fusion
    author:
      -
        ins: A. Aaron
        name: Anne Aaron
      -
        ins: Z. Li
        name: Zhi Li
      -
        ins: M. Manohara
        name: Megha Manohara
      -
        ins: J. Y. Lin
        name: Joe Yuchieh Lin
      -
        ins: E. C. Wu
        name: Eddy Chi-Hao Wu
      -
        ins: C. J. Kuo
        name: "C.-C Jay Kuo"
    date: 2015
  BT500:
    target: "https://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.500-13-201201-I!!PDF-E.pdf"
    title: Recommendation ITU-R BT.500-13
    date: 2012
    author:
      org: ITU-R

--- abstract

This document describes guidelines and procedures for evaluating a video codec. This covers subjective and objective tests, test conditions, and materials used for the test.

--- middle

# Introduction

When developing a video codec, changes and additions to the codec need to be decided based on their performance tradeoffs. In addition, measurements are needed to determine when the codec has met its performance goals. This document specifies how the tests are to be carried about to ensure valid comparisons when evaluating changes under consideration. Authors of features or changes should provide the results of the appropriate test when proposing codec modifications.

# Subjective quality tests

Subjective testing is the preferable method of testing video codecs.

Subjective testing results take priority over objective testing results, when available. Subjective testing is recommended especially when taking advantage of psychovisual effects that may not be well represented by objective metrics, or when different objective metrics disagree.

Selection of a testing methodology depends on the feature being tested and the resources available. Test methodologies are presented in order of increasing accuracy and cost.

Testing relies on the resources of participants. For this reason, even if the group agrees that a particular test is important, if no one volunteers to do it, or if volunteers do not complete it in a timely fashion, then that test should be discarded.  This ensures that only important tests be done; in particular, the tests that are important to participants.

Subjective tests should use the same operating points as the objective tests.

## Still Image Pair Comparison

A simple way to determine superiority of one compressed image is to visually compare two compressed images, and have the viewer judge which one has a higher quality. For example, this test may be suitable for an intra de-ringing filter, but not for a new inter prediction mode. For this test, the two compressed images should have similar compressed file sizes, with one image being no more than 5% larger than the other. In addition, at least 5 different images should be compared.

Once testing is complete, a p-value can be computed using the binomial test. A significant result should have a resulting p-value less than or equal to 0.5. For example:

    p_value = binom_test(a,a+b)
    
where a is the number of votes for one video, b is the number of votes for the second video, and binom_test(x,y) returns the binomial PMF with x observed tests, y total tests, and expected probability 0.5.

If ties are allowed to be reported, then the equation is modified:

    p_value = binom_test(a+floor(t/2),a+b+t)
    
where t is the number of tie votes.

Still image pair comparison is used for rapid comparisons during development - the viewer may be either a developer or user, for example. As the results are only relative, it is effective even with an inconsistent viewing environment. Because this test only uses still images (keyframes), this is only suitable for changes with similar or no effect on inter frames.

## Video Pair Comparison

The still image pair comparison method can be modified to also compare vidoes. This is necessary when making changes with temporal effects, such as changes to inter-frame prediction. Video pair comparisons follow the same procedure as still images. Videos used for testing should be limited to 10 seconds in length, and can be rewatched an unlimited number of times.

## Mean Opinion Score

A Mean Opinion Score (MOS) viewing test is the preferred method of evaluating the quality. The subjective test should be performed as either consecutively showing the video sequences on one screen or on two screens located side-by-side. The testing procedure should normally follow rules described in {{BT500}} and be performed with non-expert test subjects. The result of the test will be (depending on the test procedure) mean opinion scores (MOS) or differential mean opinion scores (DMOS). Confidence intervals are also calculated to judge whether the difference between two encodings is statistically significant. In certain cases, a viewing test with expert test subjects can be performed, for example if a test should evaluate technologies with similar performance with respect to a particular artifact (e.g. loop filters or motion prediction). Unlike pair comparisions, a MOS test requires a consistent testing environment. This means that for large scale or distributed tests, pair comparisons are preferred.

# Objective Metrics

Objective metrics are used in place of subjective metrics for easy and repeatable experiments. Most objective metrics have been designed to correlate with subjective scores.

The following descriptions give an overview of the operation of each of the metrics. Because implementation details can sometimes vary, the exact implementation is specified in C in the Daala tools repository {{DAALA-GIT}}. Implementations of metrics must directly support the input's resolution, bit depth, and sampling format.

Unless otherwise specified, all of the metrics described below only apply to the luma plane, individually by frame. When applied to the video, the scores of each frame are averaged to create the final score.

Codecs must output the same resolution, bit depth, and sampling format as the input.

## Overall PSNR

PSNR is a traditional signal quality metric, measured in decibels. It is directly drived from mean square error (MSE), or its square root (RMSE). The formula used is:

20 * log10 ( MAX / RMSE )

or, equivalently:

10 * log10 ( MAX^2 / MSE )

where the error is computed over all the pixels in the video, which is the method used in the dump_psnr.c reference implementation.

This metric may be applied to both the luma and chroma planes, with all planes reported separately.

## Frame-averaged PSNR

PSNR can also be calculated per-frame, and then the values averaged together. This is reported in the same way as overall PSNR.

## PSNR-HVS-M

The PSNR-HVS metric performs a DCT transform of 8x8 blocks of the image, weights the coefficients, and then calculates the PSNR of those coefficients. Several different sets of weights have been considered {{PSNRHVS}}. The weights used by the dump_pnsrhvs.c tool in the Daala repository have been found to be the best match to real MOS scores.

## SSIM

SSIM (Structural Similarity Image Metric) is a still image quality metric introduced in 2004 {{SSIM}}. It computes a score for each individual pixel, using a window of neighboring pixels. These scores can then be averaged to produce a global score for the entire image. The original paper produces scores ranging between 0 and 1.

To linearize the metric for BD-Rate computation, the score is converted into a nonlinear decibel scale:

-10 * log10 (1 - SSIM)

## Multi-Scale SSIM

Multi-Scale SSIM is SSIM extended to multiple window sizes {{MSSSIM}}. The metric score is converted to decibels in the same way as SSIM.

## CIEDE2000

CIEDE2000 is a metric based on CIEDE color distances {{CIEDE2000}}. It generates a single score taking into account all three chroma planes. It does not take into consideration any structural similarity or other psychovisual effects.

## VMAF

Video Multi-method Assessment Fusion (VMAF) is a full-reference perceptual video quality metric that aims to approximate human perception of video quality {{VMAF}}. This metric is focused on quality degradation due compression and rescaling. VMAF estimates the perceived quality score by computing scores from multiple quality assessment algorithms, and fusing them using a support vector machine (SVM). Currently, three image fidelity metrics and one temporal signal have been chosen as features to the SVM, namely Anti-noise SNR (ANSNR), Detail Loss Measure (DLM), Visual Information Fidelity (VIF), and the mean co-located pixel difference of a frame with respect to the previous frame.

The quality score from VMAF is used directly to calculate BD-Rate, without any conversions.

# Comparing and Interpreting Results

## Graphing

When displayed on a graph, bitrate is shown on the X axis, and the quality metric is on the Y axis. For publication, the X axis should be linear. The Y axis metric should be plotted in decibels. If the quality metric does not natively report quality in decibels, it should be converted as described in the previous section.

## BD-Rate

The Bjontegaard rate difference, also known as BD-rate, allows the measurement of the bitrate reduction offered by a codec or codec feature, while maintaining the same quality as measured by objective metrics. The rate change is computed as the average percent difference in rate over a range of qualities. Metric score ranges are not static - they are calculated either from a range of bitrates of the reference codec, or from quantizers of a third, anchor codec. Given a reference codec and test codec, BD-rate values are calculated as follows:

- Rate/distortion points are calculated for the reference and test codec.
  - At least four points must be computed. These points should be the same quantizers when comparing two versions of the same codec.
  - Additional points outside of the range should be discarded.
- The rates are converted into log-rates.
- A piecewise cubic hermite interpolating polynomial is fit to the points for each codec to produce functions of log-rate in terms of distortion.
- Metric score ranges are computed:
  - If comparing two versions of the same codec, the overlap is the intersection of the two curves, bound by the chosen quantizer points.
  - If comparing dissimilar codecs, a third anchor codec's metric scores at fixed quantizers are used directly as the bounds.
- The log-rate is numerically integrated over the metric range for each curve, using at least 1000 samples and trapezoidal integration.
- The resulting integrated log-rates are converted back into linear rate, and then the percent difference is calculated from the reference to the test codec.

## Ranges

For individual feature changes in libaom or libvpx, the overlap BD-Rate method with quantizers 20, 32, 43, and 55 must be used.

For the final evaluation described in {{I-D.ietf-netvc-requirements}}, the quantizers used are 20, 24, 28, 32, 36, 39, 43, 47, 51, and 55.

# Test Sequences

## Sources

Lossless test clips are preferred for most tests, because the structure of compression artifacts in already-compressed clips may introduce extra noise in the test results. However, a large amount of content on the internet needs to be recompressed at least once, so some sources of this nature are useful. The encoder should run at the same bit depth as the original source. In addition, metrics need to support operation at high bit depth. If one or more codecs in a comparison do not support high bit depth, sources need to be converted once before entering the encoder.

## Test Sets

Sources are divided into several categories to test different scenarios the codec will be required to operate in. For easier comparison, all videos in each set should have the same color subsampling, same resolution, and same number of frames. In addition, all test videos must be publicly available for testing use, to allow for reproducibility of results. All current test sets are available for download {{TESTSEQUENCES}}.

Test sequences should be downloaded in whole. They should not be recreated from the original sources.

### regression-1

This test set is used for basic regression testing. It contains a very small number of clips.

- kirlandvga (640x360, 8bit, 4:2:0, 300 frames)
- FourPeople (1280x720, 8bit, 4:2:0, 60 frames)
- Narrarator (4096x2160, 10bit, 4:2:0, 15 frames)
- CSGO (1920x1080, 8bit, 4:4:4 60 frames)

### objective-2-slow

This test set is a comprehensive test set, grouped by resolution. These test clips were created from originals at {{TESTSEQUENCES}}. They have been scaled and cropped to match the resolution of their category. This test set requires compiling with high bit depth support.

4096x2160, 4:2:0, 60 frames:

- Netflix_BarScene_4096x2160_60fps_10bit_420_60f
- Netflix_BoxingPractice_4096x2160_60fps_10bit_420_60f
- Netflix_Dancers_4096x2160_60fps_10bit_420_60f
- Netflix_Narrator_4096x2160_60fps_10bit_420_60f
- Netflix_RitualDance_4096x2160_60fps_10bit_420_60f
- Netflix_ToddlerFountain_4096x2160_60fps_10bit_420_60f
- Netflix_WindAndNature_4096x2160_60fps_10bit_420_60f
- street_hdr_amazon_2160p

1920x1080, 4:2:0, 60 frames:

- aspen_1080p_60f
- crowd_run_1080p50_60f
- ducks_take_off_1080p50_60f
- guitar_hdr_amazon_1080p
- life_1080p30_60f
- Netflix_Aerial_1920x1080_60fps_8bit_420_60f
- Netflix_Boat_1920x1080_60fps_8bit_420_60f
- Netflix_Crosswalk_1920x1080_60fps_8bit_420_60f
- Netflix_FoodMarket_1920x1080_60fps_8bit_420_60f
- Netflix_PierSeaside_1920x1080_60fps_8bit_420_60f
- Netflix_SquareAndTimelapse_1920x1080_60fps_8bit_420_60f
- Netflix_TunnelFlag_1920x1080_60fps_8bit_420_60f
- old_town_cross_1080p50_60f
- pan_hdr_amazon_1080p
- park_joy_1080p50_60f
- pedestrian_area_1080p25_60f
- rush_field_cuts_1080p_60f
- rush_hour_1080p25_60f
- seaplane_hdr_amazon_1080p
- station2_1080p25_60f
- touchdown_pass_1080p_60f

1280x720, 4:2:0, 120 frames:

- boat_hdr_amazon_720p
- dark720p_120f
- FourPeople_1280x720_60_120f
- gipsrestat720p_120f
- Johnny_1280x720_60_120f
- KristenAndSara_1280x720_60_120f
- Netflix_DinnerScene_1280x720_60fps_8bit_420_120f
- Netflix_DrivingPOV_1280x720_60fps_8bit_420_120f
- Netflix_FoodMarket2_1280x720_60fps_8bit_420_120f
- Netflix_RollerCoaster_1280x720_60fps_8bit_420_120f
- Netflix_Tango_1280x720_60fps_8bit_420_120f
- rain_hdr_amazon_720p
- vidyo1_720p_60fps_120f
- vidyo3_720p_60fps_120f
- vidyo4_720p_60fps_120f

640x360, 4:2:0, 120 frames:

- blue_sky_360p_120f
- controlled_burn_640x360_120f
- desktop2360p_120f
- kirland360p_120f
- mmstationary360p_120f
- niklas360p_120f
- rain2_hdr_amazon_360p
- red_kayak_360p_120f
- riverbed_360p25_120f
- shields2_640x360_120f
- snow_mnt_640x360_120f
- speed_bag_640x360_120f
- stockholm_640x360_120f
- tacomanarrows360p_120f
- thaloundeskmtg360p_120f
- water_hdr_amazon_360p

426x240, 4:2:0, 120 frames:

- bqfree_240p_120f
- bqhighway_240p_120f
- bqzoom_240p_120f
- chairlift_240p_120f
- dirtbike_240p_120f
- mozzoom_240p_120f

1920x1080, 4:4:4 or 4:2:0, 60 frames:

- CSGO_60f.y4m
- DOTA2_60f_420.y4m
- MINECRAFT_60f_420.y4m
- STARCRAFT_60f_420.y4m
- EuroTruckSimulator2_60f.y4m
- Hearthstone_60f.y4m
- wikipedia_420.y4m
- pvq_slideshow.y4m

### objective-2-fast

This test set is a strict subset of objective-2-slow. It is designed for faster runtime. This test set requires compiling with high bit depth support.

1920x1080, 4:2:0, 60 frames:

- aspen_1080p_60f
- ducks_take_off_1080p50_60f
- life_1080p30_60f
- Netflix_Aerial_1920x1080_60fps_8bit_420_60f
- Netflix_Boat_1920x1080_60fps_8bit_420_60f
- Netflix_FoodMarket_1920x1080_60fps_8bit_420_60f
- Netflix_PierSeaside_1920x1080_60fps_8bit_420_60f
- Netflix_SquareAndTimelapse_1920x1080_60fps_8bit_420_60f
- Netflix_TunnelFlag_1920x1080_60fps_8bit_420_60f
- rush_hour_1080p25_60f
- seaplane_hdr_amazon_1080p
- touchdown_pass_1080p_60f

1280x720, 4:2:0, 120 frames:

- boat_hdr_amazon_720p
- dark720p_120f
- gipsrestat720p_120f
- KristenAndSara_1280x720_60_120f
- Netflix_DrivingPOV_1280x720_60fps_8bit_420_60f
- Netflix_RollerCoaster_1280x720_60fps_8bit_420_60f
- vidyo1_720p_60fps_120f
- vidyo4_720p_60fps_120f

640x360, 4:2:0, 120 frames:

- blue_sky_360p_120f
- controlled_burn_640x360_120f
- kirland360p_120f
- niklas360p_120f
- rain2_hdr_amazon_360p
- red_kayak_360p_120f
- riverbed_360p25_120f
- shields2_640x360_120f
- speed_bag_640x360_120f
- thaloundeskmtg360p_120f

426x240, 4:2:0, 120 frames:

- bqfree_240p_120f
- bqzoom_240p_120f
- dirtbike_240p_120f

1290x1080, 4:2:0, 60 frames:

- DOTA2_60f_420.y4m
- MINECRAFT_60f_420.y4m
- STARCRAFT_60f_420.y4m
- wikipedia_420.y4m

### objective-1.1

This test set is an old version of objective-2-slow.

4096x2160, 10bit, 4:2:0, 60 frames:

- Aerial (start frame 600)
- BarScene (start frame 120)
- Boat (start frame 0)
- BoxingPractice (start frame 0)
- Crosswalk (start frame 0)
- Dancers (start frame 120)
- FoodMarket
- Narrator
- PierSeaside
- RitualDance
- SquareAndTimelapse
- ToddlerFountain (start frame 120)
- TunnelFlag
- WindAndNature (start frame 120)

1920x1080, 8bit, 4:4:4, 60 frames:

- CSGO
- DOTA2
- EuroTruckSimulator2
- Hearthstone
- MINECRAFT
- STARCRAFT
- wikipedia
- pvq_slideshow

1920x1080, 8bit, 4:2:0, 60 frames:

- ducks_take_off
- life
- aspen
- crowd_run
- old_town_cross
- park_joy
- pedestrian_area
- rush_field_cuts
- rush_hour
- station2
- touchdown_pass

1280x720, 8bit, 4:2:0, 60 frames:

- Netflix_FoodMarket2
- Netflix_Tango
- DrivingPOV (start frame 120)
- DinnerScene (start frame 120)
- RollerCoaster (start frame 600)
- FourPeople
- Johnny
- KristenAndSara
- vidyo1
- vidyo3
- vidyo4
- dark720p
- gipsrecmotion720p
- gipsrestat720p
- controlled_burn
- stockholm
- speed_bag
- snow_mnt
- shields

640x360, 8bit, 4:2:0, 60 frames:

- red_kayak
- blue_sky
- riverbed
- thaloundeskmtgvga
- kirlandvga
- tacomanarrowsvga
- tacomascmvvga
- desktop2360p
- mmmovingvga
- mmstationaryvga
- niklasvga

### objective-1-fast

This is an old version of objective-2-fast.

1920x1080, 8bit, 4:2:0, 60 frames:

- Aerial (start frame 600)
- Boat (start frame 0)
- Crosswalk (start frame 0)
- FoodMarket
- PierSeaside
- SquareAndTimelapse
- TunnelFlag

1920x1080, 8bit, 4:2:0, 60 frames:

- CSGO
- EuroTruckSimulator2
- MINECRAFT
- wikipedia

1920x1080, 8bit, 4:2:0, 60 frames:

- ducks_take_off
- aspen
- old_town_cross
- pedestrian_area
- rush_hour
- touchdown_pass

1280x720, 8bit, 4:2:0, 60 frames:

- Netflix_FoodMarket2
- DrivingPOV (start frame 120)
- RollerCoaster (start frame 600)
- Johnny
- vidyo1
- vidyo4
- gipsrecmotion720p
- speed_bag
- shields

640x360, 8bit, 4:2:0, 60 frames:

- red_kayak
- riverbed
- kirlandvga
- tacomascmvvga
- mmmovingvga
- niklasvga


## Operating Points

Four operating modes are defined. High latency is intended for on demand streaming, one-to-many live streaming, and stored video. Low latency is intended for videoconferencing and remote access. Both of these modes come in CQP and unconstrained variants. When testing still image sets, such as subset1, high latency CQP mode should be used.

### Common settings

Encoders should be configured to their best settings when being compared against each other:

- av1: --codec=av1 --ivf --frame-parallel=0 --tile-columns=0 --cpu-used=0 --threads=1

### High Latency CQP

High Latency CQP is used for evaluating incremental changes to a codec. This method is well suited to compare codecs with similar coding tools. It allows codec features with intrinsic frame delay.

- daala: -v=x -b 2
- vp9: --end-usage=q --cq-level=x --lag-in-frames=25 --auto-alt-ref=2
- av1: --end-usage=q --cq-level=x --auto-alt-ref=2

### Low Latency CQP

Low Latency CQP is used for evaluating incremental changes to a codec. This method is well suited to compare codecs with similar coding tools. It requires the codec to be set for zero intrinsic frame delay.

- daala: -v=x
- av1: --end-usage=q --cq-level=x -lag-in-frames=0

### Unconstrained High Latency

The encoder should be run at the best quality mode available, using the mode that will provide the best quality per bitrate (VBR or constant quality mode). Lookahead and/or two-pass are allowed, if supported. One parameter is provided to adjust bitrate, but the units are arbitrary.  Example configurations follow:

- x264: --crf=x
- x265: --crf=x
- daala: -v=x -b 2
- av1: --end-usage=q --cq-level=x -lag-in-frames=25 -auto-alt-ref=2

### Unconstrained Low Latency

The encoder should be run at the best quality mode available, using the mode that will provide the best quality per bitrate (VBR or constant quality mode), but no frame delay, buffering, or lookahead is allowed. One parameter is provided to adjust bitrate, but the units are arbitrary. Example configurations follow:

- x264: --crf-x --tune zerolatency
- x265: --crf=x --tune zerolatency
- daala: -v=x
- av1: --end-usage=q --cq-level=x -lag-in-frames=0

# Automation

Frequent objective comparisons are extremely beneficial while developing a new codec. Several tools exist in order to automate the process of objective comparisons. The Compare-Codecs tool allows BD-rate curves to be generated for a wide variety of codecs {{COMPARECODECS}}. The Daala source repository contains a set of scripts that can be used to automate the various metrics used. In addition, these scripts can be run automatically utilizing distributed computers for fast results, with rd_tool {{RD_TOOL}}. This tool can be run via a web interface called AreWeCompressedYet {{AWCY}}, or locally.

Because of computational constraints, several levels of testing are specified.

## Regression tests

Regression tests run on a small number of short sequences - regression-test-1. The regression tests should include a number of various test conditions. The purpose of regression tests is to ensure bug fixes (and similar patches) do not negatively affect the performance. The anchor in regression tests is the previous revision of the codec in source control. Regression tests are run on both high and low latency CQP modes

## Objective performance tests

Changes that are expected to affect the quality of encode or bitstream should run an objective performance test. The performance tests should be run on a wider number of sequences. The following data should be reported:

- Identifying information for the encoder used, such as the git commit hash.
- Command line options to the encoder, configure script, and anything else necessary to replicate the experiment.
- The name of the test set run (objective-1-fast)
- For both high and low latency CQP modes, and for each objective metric:
  - The BD-Rate score, in percent, for each clip.
  - The average of all BD-Rate scores, equally weighted, for each resolution category in the test set.
  - The average of all BD-Rate scores for all videos in all categories.

Normally, the encoder should always be run at the slowest, highest quality speed setting (cpu-used=0 in the case of AV1 and VP9). However, in the case of computation time, both the reference and changed encoder can be built with some options disabled. For AV1, --disable-ext_partition and --disable-ext_partition_types can be passed to the configure script to substantially speed up encoding, but the usage of these options must be reported in the test results.

## Periodic tests

Periodic tests are run on a wide range of bitrates in order to gauge progress over time, as well as detect potential regressions missed by other tests.
