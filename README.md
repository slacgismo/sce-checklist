# SCE GRIP Project Checklist

## Task 2: Data Export

### CYME conversion of the network to GLM

Pull the develop branch of GridLAB-D

> git clone -b develop https://github.com/slacgismo/gridlabd.git

#### How to convert CYME MDB file to GLM

For CYME MDB files

`-t cyme [OPTIONS ...]`

CYME databases can be converted to GLM model using the `-t cyme` option. Additional options can be provided as an equation using option `-p`, e.g., `-p name=value ...` and to define GLM global variables.

Options:

- `config=CSV`: specify the name of CSV for converter configuration

- `cyme-tables`: print out the required CYME tables

- `data-dir=DIR`: specify the directory for storing CSV tables (default /tmp/gridlabd/mdb-cyme2glm/INPUT_FILE_NAME)

- `equipment_mdb=MDB`: specify the MDB for equipment data (default None)

- `network-ID=ID`: specify the network ID list to be converted (default [])

- `coordinateCSV=CSV`: specify the name of CSV for saving the node geographic location data (default None)

Example:

> gridlabd -D mdb_load_options='-t cyme -o ./IEEE123-test.glm' ./IEEE123.mdb

or

> gridlabd -D mdb_load_options='-t cyme -o ./IEEE123-test.glm -p config=config.csv -p data-dir=./IEEE123_tables -p coordinateCSV=geodata.csv' ./IEEE123.mdb

### SPIDACalc export to GLM


#### Convert SPIDACalc xls to csv

OPTIONS:

- `precision=2`: specify the number of digits in a number and function ROUND will be used (default 2)

- `extract_equipment=yes`: enable the conversion of pole-mounted equipment, dummy values will be used for equipment properties (default None)

- `include_network=yes`: enable the generation of a bus-type feeder, dummy values will be used for properties of feeder and equipment (default None)

- `include_weather=NAME`: name the weather object and do not use dummy values for weather data (default None)

> gridlabd convert test_xls-spida2csv-geodata.xls DEMO_test.csv -f xls-spida -t csv-geodata extract_equipment=yes include_network=yes

#### Convert csv to GLM

> gridlabd -D csv_load_options="-f table -t object -M powerflow -o DEMO_test.glm" DEMO_test.csv

#### Run GridLAB-D pole analysis

Output options:
  
- `--output=CVSNAME`                  set the output CVS file name
                                    (default is /dev/stdout)
                                    
Analysis options:
  
- `--analysis=ANALYSIS`               set the type of pole analysis
                                    (available analysis includes worst_angle,
                                    critical_speed, and loading_scenario)

- `--pole=NAME`                       set the name of pole to be analysed
                                    (default is ALL)

- `--wind_speed=SPEED`                set the wind speed in m/s 

- `--wind_direction=DEGREE`           set the wind direction in degree 

- `--direction_increment=DEGREE`      set the wind direction swapping increment 

- `--speed_increment=SPEED`           set the wind speed swapping increment 

- `
--segment=NUMBER`                  set the number of the analysis segment 



> gridlabd pole_analysis DEMO_test_network.glm --analysis=worst_angle

> gridlabd pole_analysis DEMO_test_network.glm --analysis=critical_speed 

To analyze specific poles in the GLM file

> gridlabd pole_analysis DEMO_test_network.glm --analysis=critical_speed --poles_selected=POLENAME

> gridlabd pole_analysis DEMO_test_network.glm --analysis=loading_scenario --poles_selected=POLENAME

> gridlabd pole_analysis DEMO_test_network.glm --analysis=loading_scenario --poles_selected=POLENAME --output=CSVNAME

### Subtasks

- Finish gridlabd + openfido cyme converter unified python package implementation


## Task 3: Pole Model Validation

### Bulk Pole Analysis (Import SPIDA file and weather conditions; Run simulation of pole vulnerability based on these conditions)

Follow same procedure as Task 2 SPIDA conversion to GLM

Create poles based on a GLM

>gridlabd create_poles model.glm --output=network.glm --spacing=100 --pole_type=WOOD-EC-45/4 --include_network --weather=forecast --pole_data=POLEDATA_CSV

Add 
> #include "forecast.glm"

to GLM file

> gridlabd pole_analysis model.glm --analysis=loading_scenario --output=output.csv

### Pole Analysis with network integration (Import SPIDA file and network file; Run simulation of pole vulnerability with network specified) 

> gridlabd convert test_xls-spida2csv-geodata.xls model-test.csv -f xls-spida -t csv-geodata extract_equipment=yes include_network=yes

> gridlabd -D csv_load_options="-f table -t object -M powerflow -o model-test.glm" model-test.csv

> gridlabd pole_analysis model-test.glm --analysis=loading_scenario --output=output.csv    


## Task 6: Optimal switching algorithm validation for PSPS applications

- Run switching algorithms on a network 
- Verify accuracy of simulated load loss given state of the network under PSPS. 

### Subtasks

TODO
- Verify CBC solver is installed on docker develop image for PSPS
- Get kamerans napa feeder working on gridlabd implementation
