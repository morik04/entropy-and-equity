# entropy-and-equity
A collaborative financial modeling repository for Physics/Finance students exploring the limits of predictable equity.



### Description ###

# src\Fourier_Modeling.ipynb
Using fourier series of n harmonics to model financial assets.
Works better for commodoties such as soybeans and corn as the pattern is more periodic
Make sure to set period to 252 NOT 365 (only 252 trading days in a year)


# src\Options_Modeling.ipynb
Meant to model options using black schole equations.
Includes a rough estimator for dV using delta, gamma, theta


# tools\CSV_Handeling.ipynb
Creates csv file given ticker and date range. 
Reader also included, make sure header of csv matches or it wont work.

# data\
Includes csv files of finiancial assets. Make sure left column is labeled 'Date'
and format is YYYY-MM-DD. Right column should be 'Adj Close' and the format should be float.
