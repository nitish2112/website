+++
# Recent Publications widget.
# Note: this widget will only display if `content/publication/` contains publications.

date = "2020-11-02T00:00:00"
draft = false

title = "Collaborations with Inaccel"
subtitle = ""
widget = "talks"

# Order that this section will appear in.
weight = 4

# Number of publications to list.
count = 10

# Show publication details (such as abstract)? (true/false)
detailed_list = true
+++

## **Accelerating Face detection on a cluster of 8 Alveo FPGA cards**

[InAccel](https://inaccel.com/), a world-pioneer in the domain of FPGA-based
accelerators, has released an integrated framework that allows to utilize the
power of an FPGA cluster for face detection, see [this](https://inaccel.com/accelerated-face-detection-on-a-cluster-of-8-alveo-fpga-cards/).
Specifically, InAccel has presented a demo in which a cluster of 8 FPGAs are
used to provide up to 1700 fps (supporting up to 56 cameras with 30 fps in 
a single server). The HLS implementation used unded the hood is the
face detection design proposed in our FPGA 2017 and FPGA 2018 papers. See the
demo from inaccel below:

{{< youtube DHIzrhyDBCI >}}

