# SCE GRIP Project Checklist

## Task 2: Data Export

- CYME conversion of the network to GLM.

> git checkout -b develop-add-gridlabd-cyme-converter

> gridlabd -D mdb_load_options='-t cyme -o ./IEEE123-test.glm' ./IEEE123.mdb

> gridlabd -D mdb_load_options='-t cyme -o ./IEEE123-test.glm -p config=config.csv -p data-dir=./IEEE123_tables -p coordinateCSV=geodata.csv' ./IEEE123.mdb

- SPIDACalc export to GLM.


Convert SPIDACalc xls to csv
> gridlabd convert test_xls-spida2csv-geodata.xls DEMO_test.csv -f xls-spida -t csv-geodata

> gridlabd convert test_xls-spida2csv-geodata.xls DEMO_test.csv -f xls-spida -t csv-geodata extract_equipment=yes

> gridlabd convert test_xls-spida2csv-geodata.xls DEMO_test.csv -f xls-spida -t csv-geodata extract_equipment=yes include_network=yes

Convert csv to GLM
> gridlabd -D csv_load_options="-f table -t object -M powerflow -o DEMO_test.glm" DEMO_test.csv

Run GridLAB-D pole analysis
> gridlabd pole_analysis DEMO_test_network.glm --analysis=worst_angle

> gridlabd pole_analysis DEMO_test_network.glm --analysis=critical_speed 

> gridlabd pole_analysis DEMO_test_network.glm --analysis=critical_speed --poles_selected=POLENAME

> gridlabd pole_analysis DEMO_test_network.glm --analysis=loading_scenario --poles_selected=POLENAME

> gridlabd pole_analysis DEMO_test_network.glm --analysis=loading_scenario --poles_selected=POLENAME --output=CSVNAME

### Subtasks

- Use Fuhong use case zip folder examples to put together instructions

- Finish gridlabd + openfido cyme converter unified python package implementation


## Task 3: Pole Model Validation

- Bulk Pole Analysis (Import SPIDA file and weather conditions; Run simulation of pole vulnerability based on these conditions)
- Pole Analysis with network integration (Import SPIDA file and network file; Run simulation of pole vulnerability with network specified) 

Create poles based on a GLM

> gridlabd create_poles example.glm --output=model.glm  --spacing=100 --pole_type=WOOD-EC-45/4 --include_network --weather=example

> gridlabd create_poles example.glm --output=model.glm  --spacing=100 --pole_type=WOOD-EC-45/4 --include_network --weather=example --pole_data=POLEDATA_CSV

> gridlabd create_poles DEMO_test.glm --output=model.glm  --spacing=100 --pole_type=WOOD-EC-45/4 --include_network --weather=forecast


>gridlabd create_poles model.glm --output=network.glm --spacing=100 --pole_type=WOOD-EC-45/4 --include_network --weather=forecast

Add 
> #include "forecast.glm"

to GLM file

> gridlabd pole_analysis model.glm --analysis=loading_scenario --output=output.csv


## Task 6: Optimal switching algorithm validation for PSPS applications

- Run switching algorithms on a network 
- Verify accuracy of simulated load loss given state of the network under PSPS. 

### Subtasks

- Verify CBC solver is installed on docker develop image for PSPS
- Get kamerans napa feeder working on gridlabd implementation
