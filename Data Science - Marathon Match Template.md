### Challenge Overview

_(EXAMPLE OVERVIEW)_

Welcome to the **pDEM - Paleo-Digital Elevation Model** Marathon Match! The problem we approach today is the reconstruction of prehistoric Earth terrain (also including ocean floors) from a sparse set of geological observations that indicate the elevation and slope of terrain at certain points in geological time. These observations originate, for example, from drilling a hole in the ground, and analysing the composition of extracted material at different depths. Today creation and updates of such elevation models is a labor-intensive largely manual process performed by highly-skilled researchers, our goal is to come up with algorithms to do the same in an accurate, fast, and fully automated manner.


### DATASET DETAILS

_(EXAMPLE DATASET DETAILS)_

The training dataset is provided in the match forum, accessible after the registration. It is a CSV file with following columns:


*   **X**, and **Y** are the longitude and latitude coordinates at Earth surface in degrees according to [WGS84](https://en.wikipedia.org/wiki/World_Geodetic_System#A_new_World_Geodetic_System:_WGS_84). The points are distributed evenly on a regular rectangular grid covering the Earth's surface in a WGS84 geographic projection. You will find however, that our data are sparse, and the values in other columns are present only for some points where they are known. Your ultimate goal in this challenge is to output for each X, Y point in the input a single number: the estimated paleo-terrain level at that coordinate, expressed in meters.
*   **DEM** contains the ground truth terrain elevation values (measured in meters) for western hemisphere (X = -180 to 0; Y = -90 to 90). These are the values you aim to reconstruct for the entire globe using information from other columns. During provisional and final scoring this column will be empty in the input. Further below you will find more details on differences between training, provisional, and final datasets.
*   **Elev_value**, **Elev_error**, **Elev_min**, **Elev_max**, and **Slope_azimuth** are accurate estimations of terrain level you aim to predict, obtained from geologic observations. These are hard restrictions your solution must satisfy at the points where they are given. Specifically:
    *   **Elev_value** and **Elev_error** are the estimated paleo-terrain level and its estimation error, both in meters. At _X_, _Y_ coordinates where they are given your output terrain level estimation _Zxy_ must satisfy _|Zxy - ElevValuexy| &lt;= 2 ElevError_.
    *   **Elev_min** and **Elev_max** are the estimated lower and maximum bounds on the paleo-terrain level, both in meters. At _X_, _Y_ coordinates where either of them is given your output terrain level estimation _Zxy_ must satisfy _Zxy > ElevMinxy_ and _Zxy &lt; ElevMaxxy_.
    *   **Slope_azimuth** is the estimated direction of paleo-terrain slope, given as the angle between the direction to north, and the direction of positive terrain level gradient, measured in degrees in the clockwise direction. At _X_, _Y_ coordinates where the slope azimuth is provided, the azimuth calculated from your output terrain level must match with it within 5 degrees. \

_(SAMPLES IMAGE)_
![SAMPLE IMAGE](https://www.topcoder.com/wp-content/themes/tc3-marketing/img/rawpixel-03.png)

 \
**_Fig. 1. Geometry of the problem. X and Y axes are directed to the East and North; the points are sampled at the regular grid with equal steps dx = dy = 0.5°. The Z axis (terrain elevation) is directed upward (to the viewer). At a selected point (X, Y) the slope gradient g is the vector with coordinates _**

_(BE THOROUGH AND SPECIFIC HERE)_




### SUBMISSION FORMAT

You will submit a [Dockerized](https://docs.docker.com/get-started/) code following [this generic template](https://github.com/topcoder-platform-templates/marathon-code-only). A sample submission customized for this very match is provided in the match forum, and you are welcome to ask there for further help with the dockerization and submission format.

It should be possible to build and run your submission executing these Docker commands in the code folder of your solution:


```
docker build -t submission .
docker run --rm -v /path/to/data:/data:ro -v /path/to/workdir:/workdir submission ./test.sh /data/input.csv /workdir/output.csv
```


The entry point script of your solution, `test.sh`, will get two arguments: the path to input and output CSV files. The output file must have three columns: **X**, **Y**, **DEM**. The first two columns must match exactly the same columns of the input file; to the DEM column you should output the reconstructed terrain elevation at each point.


### SCORING DETAILS

The outputs of your submissions will be scored in three steps. A failure on the first or second step means the zero overall score.


1. **Fail or Pass**.
    1. A sanity check that your output contains exactly the same points as the input; _i.e._ the X, and Y columns in your output must match exactly the X, and Y columns from input.
    2. A sanity check that in each row (for each X, and Y) you have output a valid **DEM** number - the estimated terrain level elevation in meters.
    3. A sanity check that DEM values and its derivatives are continuous over the entire Earth surface. For that end we estimate first and second derivatives of DEM from your output and check that their absolute values at any point are smaller than 12000 m/deg for the first-order derivatives, and 12000 m/deg2 for the second-order derivatives. We also check that derivatives of each kind are bound by a smaller 4000 m/deg absolute value threshold at least at 99% of points (4000 m/deg2, in case of second-order derivatives).
2. **Fail or Pass**. We check that your solution satisfies the hard conditions on **Elev_value**, **Elev_error**, **Elev_min**, **Elev_max**, **Slope_azimuth** (explained in the Dataset Details section above), at all points where these values were provided in the input.
3. **Numeric Score.** Assuming your solution has passed the checks (1) and (2), we calculate its score based on RMS deviation of your output **DEM** from the ground-truth **DEM** within the geographic area used for scoring. \
 \

_(DESCRIBE HOW THE SCORING FORMULA WORKS HERE)_


The dockerized version of scorer is provided in the challenge forum. It can be built and executed with the following commands (assuming you keep you solution output, and the ground truth in the same workdir folder on your host machine):

```
docker build -t scorer .
docker run --rm -v /path/to/workdir:/workdir scorer ./run-scorer.sh /workdir/solution.csv /workdir/ground-truth.csv /workdir
```


As the `ground-truth.csv` you should provide the training input CSV file. The score at step #3 will be calculated over the points containing non-empty DEM values in that training input. The result score will be written out into the result.txt file in the `workdir`.



*   This match is rated (TCO-eligible), and individual (no teaming allowed).
*   The provisional and final scoring is performed at **[AWS EC2 machine](https://aws.amazon.com/ec2/instance-types/)**. The solution runtime limit is 1 hour. Solutions not completed within the runtime limit will be failed with zero score.
*   Only positive scores (anything passing the hard "fail or pass" checks) on final dataset are eligible for prizes.
*   Any 3rd party components of your solution must be available under permissive open source licenses, similar to [MIT License](https://en.wikipedia.org/wiki/MIT_License). In case of doubts, please ask in the challenge forum, or contact the match copilot directly, to approve the use of libraries licensed under more restrictive terms.
*   To claim a prize, in addition to getting a prize-eligible score in the final testing, within a week after the announcement of the final results you must submit a detailed write-up explaining your solution, its implementation, and any other details relevant to its usage and further development.
