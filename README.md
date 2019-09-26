# Code for analysis of FA reform.
- Step 1: Download, clean and combine several data:
    - WEO: World Economic Outlook database (GDP in dollars)
    - CBCR: IRS Country by Country Reporting (Profit, employees and sales of US multinationals)
    - GRD: Government Revenue Dataset (Government corporate and tax revenues)
    - Data on statutory tax rate: _Janský, Petr; Palanský, Miroslav (2018) : Estimating the scale of profit shifting and tax revenue losses related to foreign direct investment, WIDER Working Paper, No. 2018/21, ISBN 978-92-9256-463-6, The United Nations University World Institute for Development Economics Research (UNU-WIDER), Helsinki_
    - Data on population: WBD
- Step 2:
    - Create the models, where profits are allocated in terms of employees and sales, and the formulas of IMF, OECD, OECD with only 20% of residual profits apportioned, and GFA. 
- Step 3:
    - Create summary statistics per income group
- Step 4:
    - Create visualizations using:
        - All countries
        - Countries with where firms reported positive profits
        - Countries with that would obtain positive gains
        - Countries with positive profits and gains
    
----------

The models are explained below, with the formulas used to calculated the appointed profits. All formulas are of the form $AppointedP = RoutineP + ResidualP \cdot Factor$, where the factor correspond to the weights and is calculated either from the sales or the sales + employement in the jurisdiction.

P = Profits, FA = Fixed assets, Factor = weights (formulas at the end of this section).



### Models (see replication_code.ipynb to render the equations)
- “GFA”: GFA, by sales alone and by sales+employment
$$GFA_c = \sum_i{P_i}Factor_c$$
- “IMF approach”: per Valpy’s note, we estimate routine profits arising in each country in the way the IMF itself does, as a 7.5 % return on fixed assets. Residual profits are then the difference between this figure and reported profits in the BEA survey. These residual profits are summed globally, and then reallocated to each country in proportion to: (i) the country’s share in total US MNE sales in the survey; and (ii) the combined shares of sales and employment in the survey.
$$IMF_c = 0.075 FA_c + (\sum_i{P_i}-0.075 \sum_i{FA_i}) Factor_c$$
- “OECD approach”: in fact a hybrid of the IMF and OECD approaches, following the BEPS Monitoring Group note. While the original OECD approach was to designate profits in ‘havens’ as ‘residual’, and then apportion these, the hybrid is as follows. First, identify routine profits at the global level as a 7.5% return on fixed assets. This value is attributed between jurisdictions according to arm’s length pricing: that is, for our purposes they are distributed between jurisdictions according to the current proportions of taxable profit. Second, the ‘residual’ profit (i.e the remaining global value of profits that is not covered by the return on fixed assets) is then apportioned between jurisdictions according to sales/sales+employment.
$$OECD_c = 0.075 FA_c \frac{P_c}{\sum_i{P_i}} + (\sum_i{P_i}-0.075 \sum_i{FA_i})Factor_c$$
- “OECD 20% approach”: First, identify routine profits at the global level as a 7.5% return on fixed assets. Then add 80% of the remaining ‘residual’ profits. This value is attributed between jurisdictions according to arm’s length pricing: that is, for our purposes they are distributed between jurisdictions according to the current proportions of taxable profit. Second, the remaining 20% of ‘residual’ profit (i.e the remaining global value of profits that is not covered by the return on fixed assets) is then apportioned between jurisdictions according to sales/sales+employment. 

$$OECD(20)_c = 0.075 FA_c \frac{P_c}{\sum_i{P_i}} + 0.8(\sum_i{P_i}-0.075 \sum_i{FA_i}) \frac{P_c}{\sum_i{P_i}} + 0.2(\sum_i{P_i}-0.075 \sum_i{FA_i}) Factor_c$$

Where $Factor_c$ corresponds to $\frac{Sales_c}{\sum_i{Sales_i}}$ or $\frac{1}{2}(\frac{Sales_c}{\sum_i{Sales_i}} + \frac{Employment_c}{\sum_i{Employment_i}})$ for the two formulas.


------------

- Requirements:
    - Python3 and data processing libraries. The easier way to get all the requirements is to install Anaconda: https://www.anaconda.com/distribution/
    - The interactive visualizations require the library altair. If Anaconda is installed you can install altair running: conda install -c conda-forge altair vega_datasets notebook vega
    - The scatter plots use adjusttext (https://github.com/Phlya/adjustText) to avoid overlaps between the labels, install with: conda install -c phlya adjusttext 
    
- Usage:
    - Update the links ib replication_code.ipynb with the most recent version of the data. Reflect the changes in the file names as well. Please note that the code will break if the format of the files changes at some point (e.g. change in column names of WEO or CBCR or change in column order for GRD). 
    - Run the rest of the code in order
    

## Structure of the folders
- replication_code.ipynb: Code in a jupyter notebook
- Data: Original and cleaned datasets
- Sep19: Analysis of this date. 
    - The four versions (keeping all countries, only countries where firms reported positive profits, only countries with potential positive gains, only countries with positive profits and ngains)
        - Figures: All figures
        - Tables: All tables
            - Within both tables and figures the prefix "groups" refers to the data aggregated at the group level (G20, G7, G77, EU, US, OECD, etc), "income" refers to to the income groups, "regions" at the region level, "individual_countries" corresponds to all countries.
            - The suffix "M" are the total profits, "M_tax" the total gains, "M_%cit" 100 * the gains divided by corporate tax revenues, "M_%gdp" 100 * the gains divided by GDP, "M_%tot_tax" 100* the gains divided by total tax revenue, "M_%pop" the gains divided by population.