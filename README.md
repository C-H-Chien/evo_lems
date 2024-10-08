# evo LEMS research @ Brown University
This is a forked repo from [evo](https://github.com/MichaelGrupp/evo). <br />
Edited initially in needs of evaluating visual odometry algorithms in the MIS environment for the internal LEMS lab research. It is however applicable to any evaluation needs for VO/SLAM pipelines. The entire evaluation tool has been tested in Ubuntu systems. <br />

# Quick and Simple Installation
Simply follow the official evo instruction, _i.e._, <br />
```bash
pip install evo --upgrade --no-binary evo
```
Note that you might be able to use `pip2` or `pip3` to specify your Python version.
Also make sure to use the root authority so that the commands are installed in the system.

# Common Evaluation Metrics
* **Relative Trajectory Error (RPE)**
```bash
evo_rpe <data_format> <groundtruth_file_name>.txt <estimated_poses_file_name>.txt -r <target_metric> --delta <n>
```
Input arguments: <br />
	- ``<data_format>`` can be either ``kitti``, ``tum``, or ``euroc``, depending on how the camera poses are structured in the files. <br />
	- ``<groundtruth_file_name>`` is the file name of ground-truths you wish to compare the estimated pose with. <br />
	- ``<estimated_poses_file_name>`` is the file name of estimations you wish to compare with the ground-truths. <br />
	- ``<target_metric>`` can be either ``full`` (both translation and rotation parts), ``trans_part`` (only translation, user-defined unit), ``rot_part`` (only rotation part, unitless), ``angle_deg`` (only rotation part in degree), and ``angle_rad`` (only rotation part in radian). <br />
	- ``<n>`` is a positive integer indicating the number of frames apart for evaluating RPE, _i.e._, ``--delta 1`` evaluates RPE on consecutive frames. <br />
	
* **Absolute Trajectory Error (ATE)**
Different from the original EVO repository, we allow 
	- **Trajectory alignment at the origin** in order to observe the extent of cumulative errors from difference algorithms. <br />
	- **Normalize the result by the trajectory path length** in order to compare results across different sequences with different path lengths. <br />
```bash
evo_ape <data_format> <groundtruth_file_name>.txt <estimated_poses_file_name>.txt --<alignment_method> --correct_scale --normalize_by_path_length -r <target_metric>
```
Input arguments: <br />
	- ``<data_format>``, ``<groundtruth_file_name>``, ``<estimated_poses_file_name>``, and ``<target_metric>`` are the same as RPE. <br />
	- ``<alignment_method>`` is the method for aligning the estimated trajectory to the ground-truth trajectory, including _(i)_ ``align_origin`` which aligns two trajectories by fixing the first frame to the same coordinate, _(ii)_ ``align`` which aligns two trajectory using Horn's method.  <br />


# Visualizing the trajectory
Visualizing multiple trajectories can be achieved via
```bash
evo_traj <data_form> --ref ./<ground-truth file>.txt ./<trajectory file1>.txt ./<trajectory file2>.txt -p --plot_mode xyz
```
where you can add as many ``<trajectory file>.txt`` as possible. Typically, I would suggest aligning the trajectories to the origin by adding ``--align_origin`` flag so that it reflects the camera motion drift. Also, if the scale is inconsistent between the ground-truth and the estimated trajectory, add ``--correct_scale`` flag to resolve it.

# Change Logs
[Jun. 26th, 2023] Enable input flags `align_origin` and `correct_scale` effective at the same time. <br />
[Jul. 5th, 2023]  Add normalized by the number of frames. <br />
[Jul. 28th, 2023] Add normalized by the path length. <br />
[Aug. 30th, 2024] Improve the quality of visualizing trajectories. <br />
<br />
More updates are coming soon.


------------------------------------------------------------------------------
# evo 

***Python package for the evaluation of odometry and SLAM***

This package provides executables and a small library for handling, evaluating and comparing the trajectory output of odometry and SLAM algorithms.

Supported trajectory formats:

* 'TUM' trajectory files
* 'KITTI' pose files
* 'EuRoC MAV' (.csv groundtruth and TUM trajectory file)
* ROS and ROS2 bagfile with `geometry_msgs/PoseStamped`, `geometry_msgs/TransformStamped`, `geometry_msgs/PoseWithCovarianceStamped` or `nav_msgs/Odometry` topics or [TF messages](https://github.com/MichaelGrupp/evo/wiki/Formats#bag---ros-bagfile)

See [here](https://github.com/MichaelGrupp/evo/wiki/Formats) for more infos about the formats.

<a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_map.png" target="_blank">
  <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_map.png" alt="evo" height="200" border="5" />
</a>
<a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_violin.png" target="_blank">
  <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_violin.png" alt="evo" height="200" border="5" />
</a>
<a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/markers.png" target="_blank">
  <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/markers.png" alt="evo" height="200" border="5" />
</a>
<a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_stats.png" target="_blank">
  <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_stats.png" alt="evo" height="200" border="5" />
</a>

---

## Why?

evo has several advantages over other public benchmarking tools:

* common tools for different formats
* algorithmic options for association, alignment, scale adjustment for monocular SLAM etc.
* flexible options for output, [plotting](https://github.com/MichaelGrupp/evo/wiki/Plotting) or export (e.g. LaTeX plots or Excel tables)
* a powerful, configurable CLI that can cover many use cases
* modular `core` and `tools` libraries for custom extensions
* faster than other established Python-based tools ([see here](https://github.com/MichaelGrupp/evo/blob/master/doc/performance.md))

**What it's not:** a 1-to-1 re-implementation of a particular evaluation protocol tailored to a specific dataset. 

---

## Installation / Upgrade

Installation is easy-peasy if you're familiar with this: https://xkcd.com/1987/#

evo supports **Python 3.8+**. The last evo version that supports **Python 2.7** is `1.12.0`.
You might also want to use a [virtual environment](https://github.com/MichaelGrupp/evo/blob/master/doc/install_in_virtualenv.md).

### From PyPi
If you just want to use the executables of the latest release version, the easiest way is to run:
```bash
pip install evo --upgrade --no-binary evo
```
This will download the package and its dependencies from [PyPI](https://pypi.org/project/evo/) and install or upgrade them. Depending on your OS, you might be able to use `pip2` or `pip3` to specify the Python version you want. Tab completion for Bash terminals is supported via the [argcomplete](https://github.com/kislyuk/argcomplete/) package on most UNIX systems - open a new shell after the installation to use it (without `--no-binary evo` the tab completion might not be installed properly). If you want, you can subscribe to new releases via https://libraries.io/pypi/evo.

### From Source
Run this in the repository's base folder:
```bash
pip install --editable . --upgrade --no-binary evo
```

### Dependencies

**Python packages**

evo has some required dependencies that are ***automatically resolved*** during installation with pip.
They are specified in the `install_requires` part of the `setup.py` file.

**PyQt5 (optional)**

PyQt5 will give you the enhanced GUI for plot figures from the "*Qt5Agg*" matplotlib backend (otherwise: "*TkAgg*"). If PyQt5 is already installed when installing this package, it will be used as a default (see `evo_config show`). To change the plot backend afterwards, run `evo_config set plot_backend Qt5Agg`.

**ROS (optional)**

Some ROS-related features require a ROS installation, see [here](http://www.ros.org/). We are testing this package with ROS Noetic and Galactic. Previous versions (`<= 1.12.0`) work with Melodic, Kinetic and Indigo.

---

## Command Line Interface

After installation with setup.py or from pip, the following executables can be called globally from your command-line:

**Metrics:**

* `evo_ape` - absolute pose error
* `evo_rpe` - relative pose error

**Tools:**

* `evo_traj` - tool for analyzing, plotting or exporting one or more trajectories
* `evo_res` - tool for comparing one or multiple result files from `evo_ape` or `evo_rpe`
* `evo_fig` - (experimental) tool for re-opening serialized plots (saved with `--serialize_plot`)
* `evo_config` - tool for global settings and config file manipulation

Call the commands with `--help` to see the options, e.g. `evo_ape --help`. Tab-completion of command line parameters is available on UNIX-like systems.

**More documentation**
Check out the [Wiki on GitHub](https://github.com/MichaelGrupp/evo/wiki).

---

## Example Workflow

There are some example trajectories in the source folder in `test/data`.


### 1.) Plot multiple trajectories

  Here, we plot two KITTI pose files and the ground truth using `evo_traj`:
  ```
  cd test/data
  evo_traj kitti KITTI_00_ORB.txt KITTI_00_SPTAM.txt --ref=KITTI_00_gt.txt -p --plot_mode=xz
  ```

  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/traj_demo.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/traj_demo.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/traj_demo_xyz.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/traj_demo_xyz.png" alt="evo" height="200" border="5" />
  </a>

### 2.) Run a metric on trajectories

  For example, here we calculate the absolute pose error for two trajectories from ORB-SLAM and S-PTAM using `evo_ape` (`KITTI_00_gt.txt` is the reference (ground truth)) and plot and save the individual results to .zip files for `evo_res`:

  *First trajectory (ORB Stereo):*

  ```
  mkdir results
  evo_ape kitti KITTI_00_gt.txt KITTI_00_ORB.txt -va --plot --plot_mode xz --save_results results/ORB.zip
  ```

  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_raw.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_raw.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_map.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_ORB_map.png" alt="evo" height="200" border="5" />
  </a>

  *Second trajectory (S-PTAM):*
  
  ```
  evo_ape kitti KITTI_00_gt.txt KITTI_00_SPTAM.txt -va --plot --plot_mode xz --save_results results/SPTAM.zip
  ```

  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_S-PTAM_raw.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_S-PTAM_raw.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_S-PTAM_map.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/ape_demo_S-PTAM_map.png" alt="evo" height="200" border="5" />
  </a>


### 3.) Process multiple results from a metric

  `evo_res` can be used to compare multiple result files from the metrics, i.e.:
  * print infos and statistics (default)
  * plot the results
  * save the statistics in a table

  Here, we use the results from above to generate a plot and a table:
  ```
  evo_res results/*.zip -p --save_table results/table.csv
  ```

  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_raw.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_raw.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_dist.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_dist.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_stats.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_stats.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_box.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_box.png" alt="evo" height="200" border="5" />
  </a>
  <a href="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_violin.png" target="_blank">
    <img src="https://raw.githubusercontent.com/MichaelGrupp/evo/master/doc/assets/res_violin.png" alt="evo" height="200" border="5" />
  </a>

---

## IPython / Jupyter Resources

For an interactive source code documentation, open the [Jupyter notebook](http://jupyter.readthedocs.io/en/latest/install.html) `metrics_tutorial.ipynb` in the `notebooks` folder of the repository. More infos on Jupyter notebooks: see [here](https://github.com/MichaelGrupp/evo/blob/master/doc/jupyter_notebook.md)

If you have IPython installed, you can launch an IPython shell with a custom evo profile with the command `evo_ipython`.

---

## Contributing Utilities

A few "inoffical" scripts for special use-cases are collected  in the `contrib/` directory of the repository. They are inofficial in the sense that they don't ship with the package distribution and thus aren't regularly tested in continuous integration.

---

## Trouble
*":scream:, this piece of :shit: software doesn't do what I want!!1!1!!"*

**First aid:**
* append `-h`/ `--help` to your command
* check the [Wiki](https://github.com/MichaelGrupp/evo/wiki)
* check the [previous issues](https://github.com/MichaelGrupp/evo/issues?q=is%3Aissue+is%3Aclosed)
* open a [new issue](https://github.com/MichaelGrupp/evo/issues)

---

## Contributing

Patches are welcome, preferably as pull requests.

## License

[GPL-3.0 or later](https://www.gnu.org/licenses/gpl-3.0.html)

If you use this package for your research, a footnote with the link to this repository is appreciated: `github.com/MichaelGrupp/evo`.

...or, for citation with BibTeX:
```
@misc{grupp2017evo,
  title={evo: Python package for the evaluation of odometry and SLAM.},
  author={Grupp, Michael},
  howpublished={\url{https://github.com/MichaelGrupp/evo}},
  year={2017}
}
```
