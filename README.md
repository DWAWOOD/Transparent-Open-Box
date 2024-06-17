# Transparent-Open-Box
**Explanations relating to transparent open box (TOB) code files**
**1.	History of the TOB algorithm** 
The transparent open box (TOB) machine learning algorithm was originally developed in 2018:
Wood, D.A., 2018. Transparent Open-Box learning network provides insight to complex systems and a performance benchmark for more-opaque machine learning algorithms. Advances in Geo-Energy Research, 2(2): 148-162. https://doi.org/10.26804/ager.2018.02.04
The key formulas for the TOB algorithm were published in 2019:
Wood, D.A., 2019. Transparent open-box learning network provides auditable predictions for coal gross calorific value. Modeling Earth Systems and Environment. 5, 395-419. Published online 18Nov2018 DOI: 10.1007/s40808-018-0543-9 
The TOB algorithm was originally developed with relatively small datasets in mind (e.g. several hundred data records and <10 variables). However, test showed that it also performed well with medium sized datasets (several thousand data records and fifteen or so variables (see for example the second citation). However, for much larger datasets computational cost made it inefficient.
The original algorithm was developed in Excel driven by VBA code. Excel was selected because a key aim was to make all intermediate calculations accessible, predictions explainable and auditable for data mining purposes. This included placing all the top ranking data matches associated with each prediction on specific worksheets and the weights (W) applied to them in the optimum solution. This high level of transparency and explainability is of great value for data mining purposes and for interrogating and interpreting/auditing outlying predictions and any data record of interest.
Subsequently, the algorithm has been reconfigured in Python preserving its transparency and recording of the intermediate calculations and prediction details. The algorithm has also been improved to offer the alternative of measuring the distances between data records either using the sum of the squared differences (distances) between each input variable (P=2) or the sum of the absolute differences (distances) between each input variable (P=1). In the original algorithm P was fixed at 2. Depending on the dataset being evaluated, either P=1 or P=2 may provide the most accurate predictions.

**2.TOB Execution steps**
The TOB machine learning model has two distinct and sequential stages of execution (Stage 1 and Stage 2). TOB Stage 1 conducts a basic data matching and ranking to establish the ten closest-matching data records (Q=10) for each data record using constant variable weights. TOB Stage 2 involves optimization by applying a range of input variable weights (0<=Wi<=1) and varying Q (2<=Q<=10; where Q is an integer) to the top-ten data matches established by TOB Stage 1 to make its target variable predictions.

**2.1 TOB Stage 1 procedures**
TOB stage 1 can be used on as a standalone prediction algorithm, particularly for data mining and prediction outlier investigation. It does make credible predictions in its own right but its predictions are usually outperformed by the TOB Stage 2 optimized predictions. TOB Stage 1 has Q set equal to 10, so the ten closes matches are always used in is predictions, although it is configured to establish the top 24 closest matching records. Also, its Wi (input variable weights are all set equal to a value greater than 0 and <=1). If Wi for any variable is set equal to 0 then that variable is excluded from the prediction calculations. Each data record match results in a distance measure, either the sum of the squared distances (P=2) between each variable or the sum of their absolute distances (P=1).
It calculates its predictions based on its unweighted input variable selection of the ten closest matches using their relative inverse distance value to calculate the magnitude of influence each of the closest-matching records has on the prediction of each data record. 
The TOB Stage 1 algorithm has the option to save its intermediate calculations (top matching data records;  sum of distances for each of the closest matching data records; actual versus predicted target values) as a .csv file.
The format of that file (“**topmatchbyrow.csv**”) displays information for each data record in columns. This represents full disclosure of all the intermediate calculation results for each data record. The information in the rows is as follows:

Row 149: actual dependent variable value denormalized
Row 148: predicted dependent variable value denormalized
Row 147: actual dependent variable value normalized
Row 146: predicted dependent variable value normalized 
Rows 120 to 144: dependent variable normalized value of top 25 matching records ranked
Rows 103 to 112: contribution of each top 10 ranked matches to prediction
Row 101: sum of distance values for Q matching records (i.e. sum of values in rows  89 to 98)
Rows 89 to 98: for top 10 matches divided by value in row 87 (used in F calc to determine relative contribution to predictions)
Row 87: sum of distances for each Q matching record makes to prediction (used for F calc)
Rows 61 to 84: top 24 sum of distances of matching data records from record being matched 
Rows 31 to 54: top 24 matching data record numbers for record being matched
Row 2 to 25: top 24 matching data record numbers by sequence position  
Row 1: Data record identifier being matched (M data records; one in each column)

The TOB Stage 1 algorithm assesses the prediction performance comparing the actual and predicted dependent (target) variable values (i.e. the differences (residuals) between rows 149 and 148 in the “topmatchbyrow.csv” file. It generates RMSE, MAE and R2 values based on that assessment.
A crucial output of the TOB Stage 1 algorithm is the “**Stage2fmt.csv**” because this is used as the input to the TOB stage 2 algorithm. It includes the normalised inputs of all the variables (independent and dependent) for each data record followed by the top ten matching data records ranked in order (closest TOB Stage1 match first). Hence, for each data record there are 11 rows of input with N+1 (independent plus dependent variables) columns commencing from column 3. Column 2 lists the data record labels of each of the top matching records. Column 1 lists the fractional contributions those data records make to the prediction of the data record matched based on TOB Stage 1 assumptions (Q=10; Wi values are all equal). 
The Stage2fmt.csv file output by the TOB Stage 1 algorithm can have its first row of zeros changed to the alphanumeric names of the input variables starting in row C1 and the code for the dependent variable added in he firs row above the final column of data. This information renders the TOB Stage 2 input file more interpretable.
Three Python code sequences are provided for the TOB Stage 1 algorithm; “**1.TOB Stage 1 Base Code**” provides a sequential code generating a single solution; “**2.TOB Stage 1 Function**” provides the code in the form of a function that can be executed for multiple scenarios; and “**3.TOB Stage 1 Loop**”  provides the function configured to evaluate a “leave-one-out” scenario such as feature influence/selection.

**2.2 TOB Stage 2 procedures**
TOB Stage 2 applies optimizer(s) to the TOB Stage 1 solution in attempts to improve it by varying the input variable weights (0<=Wi<=1) and varying Q (2<=Q<=10; where Q is an integer). The SciKit optimizer (scikit-opt) library of Python optimizers offer a useful set of optimizers (see: https://scikit-opt.github.io/).
The particle swarm optimizer (sko.PSO), simulated annealing (sko.SA; selecting the SAFast option), genetic algorithm (sko.GA) and differential evolution (sko.DE) are effective optimizers to apply with TOB. Prudent to apply more than one optimizer and compare their results and execution times. The different optimizer algorithms typically find somewhat different “optimum” solutions, and comparing the Wi values of those solutions provides some useful insight to the relative importance of the input variables to each of those optimizers. The time the optimizers take to execute depends on the size of the dataset (number of data records (M) and independent variables (N)). As the datasets get larger the execution times increase. It is necessary to set the control parameters for the optimizers (e.g. for PSO number of particles and number of iterations). The higher these values the longer the optimization algorithms take to execute, but the more likely they are to find better solutions.
The algorithm’s outputs of the optimizer TOB Stage 2 code loop are the optimum Q value (between 2 and 10) and the Wi matrix values (between 0 and 1) and the optimized objective function (RMSE) which can be changed to another metric (e.g. MAE) if required. 
The optimum TOB Stage 2 solutions can then be individually evaluated, as required, with the TOB Stage 2 solution evaluation code to provide calculation details. This outputs RMSE, MAE and R2 values for the TOB Stage 2 solution based on the assessment of those predicted and actual dependent variable values. These details can be saved to two .csv files:
**TOBStage2Details.csv**: data matches and their contribution details to each data record prediction.
**TOBStage2Results.csv**: consists of three columns (one row for each data record predicted): 1) data record numbers; 2) actual dependent variable values; and, 3) predicted dependent variable values. This file is useful for calculating other prediction performance metrics in addition to RMSE, MAE and R2 if required.
The detailed data matching information in file **TOBStage2Details.csv** arranges the contents of matrix TOB2 into rows and columns in this .csv file. TOB2 is a combination of the input file (Stage2fmt.csv) and the TOB Stage 2 solution being evaluated. The first row of TOBStage2Details.csv contains important input information about the solution: cell A1 = number of data records (M); cell B1 = number of input variables (N); cell C1 = Q value of the solution being evaluated; cell D1 = minimum dependent variable value; cell E1 = maximum dependent variable value; and cellF1 = P value (1 or 2). The second row contains the TOB Stage two weights (Wi) applied to each of the input variables, beginning in order starting in cell C2 and continuing in subsequent columns for the N input variables.
The remaining rows of the TOBStage2Details.csv files consist of 11 rows for each data prediction. The first row for each data record being predicted consists of the data record name in column A followed by the normalized values of each of its variables commencing in column C. The subsequent ten rows for each predicted data record list the top-ten matching data records (named in column B) identified by TOB Stage 1 and ordered in descending order of their contributions to the TOB Stage 1 predictions (listed in column A). Beginning in column C of these rows are the weighted distance differences between each input variable of each matching record and the data record being predicted. The actual normalized value of the dependent variable for each matching data record is listed in the column following the distance difference for each input variable.
Working from the right side of file TOBStage2Details.csv:
The final column is the absolute error between predicted and actual dependent variable values for each data record being predicted.
The second to last column is the squared error between predicted and actual dependent variable values for each data record being predicted.
The third to last column is the actual dependent variable values (denormalized) for each data record being predicted.
The fourth to last column is the predicted dependent variable values (denormalized) for each data record.
The fifth to last column lists the actual normalized value contribution made by each of the top-ten matching variables to the prediction of each data record being predicted. The sum of those normalized contributions is displayed in the upper row on the left of the denormalized predicted value for that data record.
The sixth to last column lists the fractional contributions that each of the matching data records make to each the prediction of each data record being predicted. The sum of those fractions is 1. The two rows to the left of this row show the inverse weighted distance calculations from which the fractional contributions are derived. The next row to the left is the actual normalized value of the dependent variable for each matching data record.
From this large quantity of information for data mining purposes, it is the information in the column A (TOB Stage 1 fractional contributions) column B (list of top matching variables) and the sixth to last column from the right (TOB Stage 2 fractional contributions) of the TOBStage2Details.csv file that is the most useful. This can be extracted to a small spreadsheet for detailed analysis.
Thre Python code sequences are provided for the TOB Stage 2 algorithm; “**4. TOB Stage 2 Base Code**” provides a sequential code to evaluate a single specified solution (Q and Wi inputs) applied to a TOB Stage 1 output; “**5. TOB Stage 2 Function Single Solution**” also evaluates a single solution but with the code configured as a function; and, “**6. TOB Stage 2 Function for Optimization**” provides the coded configured as a function that can called and looped as part of an optimization routine to optimize a TOB Stage 1 output.

**License**
The Python codes provided in this project are protected by the GNU AFFERO GENERAL PUBLIC LICENSE. 

