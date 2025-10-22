# Steerable Needle Robustness Metric



## Motion Planning for Steerable Needles

Steerable needles are highly flexible medical devices able to follow 3D curvilinear trajectories inside the human body, reaching clinically significant targets while safely avoiding critical anatomical structures. Compared with traditional rigid-medical instruments, steerable needles can reduce a patient’s trauma, increase safety, and provide minimally invasive access to targets that were previously inaccessible. Steerable needles have been considered in a wide range of diagnostic and treatment procedures including biopsy, and radioactive seed implantation for cancer treatment.

Automating steerable needle procedures can enable physicians and patients to harness the full potential of steerable needles by maximally leveraging their steerability and ability to accurately and precisely reach targets. Automation is critical to harnessing the full potential of these needles since the non-holonomic constraints on the needle’s 3D motion coupled with the cluttered nature of anatomical environments make direct manual control un-intuitive and impractical for human operators. To automate steerable needle procedures, physicians first obtain a medical image (such as a CT scan or MRI) of the relevant anatomy, from which we can segment (manually or automatically) the relevant anatomy, including the target to reach and obstacles to avoid. The next key ingredient to the automation of steerable needle procedures is motion planning, which requires computing feasible motions to steer the needle safely around the anatomical obstacles and to the target.

## Insertion Robustness Metric

A steerable needle deployment typically consists of a physician manually placing a steerable needle at a precomputed start pose on the surface of tissue and handing off control to a robot, which then autonomously steers the needle through the tissue to the target. The handoff between humans and robots is critical for procedure success, as even small deviations from a planned start pose change the
steerable needle's reachable workspace. Our metric is based on a novel geometric
method to efficiently compute how far the physician can deviate from
the planned start pose in both position and orientation such that the
steerable needle can still reach the target. We evaluate our metric
through simulation in liver and lung scenarios. Our evaluation shows that our metric can be applied to plans computed by different steerable needle motion planners and that it can be used to efficiently select plans with large safe start regions.

## Requirements

* C++17 compatible compiler (GCC 7+ for Linux, Clang 5+ for maxOS)
* [CMake](https://cmake.org/) 3.8+
* [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) 3.3+
* [Boost](https://www.boost.org/) 1.68+
* [For visualization only] [Python](https://www.python.org/) 3.6-3.8
* [For visualization only] [Open3d](http://www.open3d.org/)

### Installing Dependencies on Ubuntu with [apt](http://manpages.ubuntu.com/manpages/bionic/man8/apt.8.html)

```
sudo apt install cmake libeigen3-dev libboost-all-dev [python3.8]
[pip3 install open3d]
```

### Installing Dependencies on macOS with [Homebrew](https://brew.sh/)

```
brew install cmake eigen boost [python@3.8]
[pip3 install open3d]
```

## Usage

### Download

```
git clone git@github.com:UNC-Robotics/steerable-needle-planner.git [{YOUR_LOCAL_REPO}]
cd {YOUR_LOCAL_REPO}
git submodule update --init --recursive
```

### Build

```
mkdir -p {YOUR_LOCAL_REPO}/build
cd {YOUR_LOCAL_REPO}/build
cmake ..
make
```

### Run

This repository contains several different needle planners:
* An RRT-based planner (referred to as RRT) initially proposed by Patil et al. [1]
* A planner based on AO-RRT [2] that is adapted for steerable needles.
* A search-based *resolution-complete* planner (referred to as RCS) initially proposed by Fu et al. [3]
* A search-based *resolution-optimal* planner (referred to as RCS\*) initially proposed by Fu et al. [4]

There are several test applications already provided, they all read from `{YOUR_LOCAL_REPO}/data/input/*` for environment information, needle parameters, and start/goal poses. By default, the planner saves the search tree to `data/output/{DATE_AND_TIME}_ptcloud.txt` and saves the best solution plan to `data/output/{DATE_AND_TIME}_interp.txt`.

Before running any of the test applications, run
```
mkdir -p {YOUR_LOCAL_REPO}/data/output
```
since the test applications will save results in the above directory.

* Plan from start to goal using the RRT planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/rrt [if_constrain_goal_orientation] [if_multi_threading] [random_seed] [tag]
```

* Plan from start to goal using the AO-RRT planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/aorrt [if_constrain_goal_orientation] [if_multi_threading] [random_seed] [tag]
```

* Plan from start to goal using the RCS planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/rcs [if_constrain_goal_orientation] [if_multi_threading] [random_seed] [tag]
```

* Plan from start to goal using the RCS* planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/rcs_star [if_constrain_goal_orientation] [if_multi_threading] [random_seed] [tag]
```

For the above test applications, the planner will run for 1 second and collect all solution plans generated. Checkout `include/test_utils.h` for different termination conditions.

* Plan from start (with no orientation constraint) to goal regions, using the RRT planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/rrt_spreading [if_multi_threading] [random_seed] [tag]
```

* Plan from start (with no orientation constraint) to goal regions, using the AO-RRT planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/aorrt_spreading [if_multi_threading] [random_seed] [tag]
```

* Plan from start (with no orientation constraint) to goal regions, using the RCS planner:
```
cd {YOUR_LOCAL_REPO}/build
./app/rcs_spreading [if_multi_threading] [random_seed] [tag]
```

For the above test applications, the planner will run for 50 seconds and collect all solution plans that get close enough (within 3mm) to the goal points.

### Quick Visualization

The search tree and plans produced by a planner can be visualized as point clouds using `scripts/ptcloud_vis.py`. It requires `Open3d`. Currently, `Open3d` supports python 3.6, 3.7, and 3.8, but does not support python 3.9. Use the following command to run it:
```
python3 {YOUR_LOCAL_REPO}/scripts/ptcloud_vis.py ptcloud_0 [ptcloud_1] [ptcloud_2] ...
```
where `ptcloud_x` are the files saving the point clouds. For example, `python3 {YOUR_LOCAL_REPO}/scripts/ptcloud_vis.py {YOUR_LOCAL_REPO}/data/input/goal_regions.txt` will visualize all goal points as a point cloud.


### References

[1] Hoelscher, J., Fried, I., Tsalikis, S., Akulian, J., Webster, R. J., and Alterovitz, R., 2025. Resolution-Optimal Safe Start Regions for Medical Steerable Needle Automation. IEEE Transactions on Robotics (presented at IROS 2025).

[2] Fu, M., Solovey, K., Salzman, O. and Alterovitz, R., 2022. Resolution-Optimal Motion Planning for Steerable Needles. IEEE International Conference on Robotics and Automation, 2022.

[3] Patil, S., Burgner, J., Webster, R.J. and Alterovitz, R., 2014. Needle steering in 3-D via rapid replanning. IEEE Transactions on Robotics, 30(4), pp.853-864.



## Citation

If you use this source code, please cite the following papers accordingly:
```
@ARTICLE{Hoelscher2025_TRO,
  author={Hoelscher, Janine and Fried, Inbar and Tsalikis, Spiros and Akulian, Jason and Webster, Robert J. and Alterovitz, Ron},
  journal={IEEE Transactions on Robotics}, 
  title={Safe Start Regions for Medical Steerable Needle Automation}, 
  year={2025},
  volume={41},
  number={},
  pages={2424-2440},
  keywords={Needles;Planning;Measurement;Robustness;Robots;Medical services;Liver;Lungs;Biopsy;Uncertainty;Motion and path planning;nonholonomic motion planning;surgical robotics: Planning;surgical robotics: Steerable catheters/needles},
  doi={10.1109/TRO.2025.3552323}}

@inproceedings{Fu2022_ICRA,
    author={Mengyu Fu and Kiril Solovey and Oren Salzman and Ron Alterovitz},
    title={{Resolution-Optimal Motion Planning for Steerable Needles}},
    booktitle = {2022 IEEE International Conference on Robotics and Automation (ICRA)},
    year={2022},
    volume={},
    number={},
    pages={9652-9659}
}
```
