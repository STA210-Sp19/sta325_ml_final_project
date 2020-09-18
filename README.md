# sta325_ml_final_project

## Introduction

### (i) Background Information and Relevant Information
The Opioid Crisis in the United States is a phenomenon that has recently gotten a plethora of media attention. Opioids is the term used for the addictive painkilling drugs that has caused, in some regards, a national epidemic. In recent years, the rates of opioid drug overdose deaths have increased significantly, particularly in the less affluent states of the United States. For purposes of distinction, synthetic opioids are the legally prescribed painkillers such as Oxycodone and Hydrocodone, but will be referred to opioids (in general) from here on out. As a result of this crisis, it is crucial to understand the factors that cause certain populations around the country to have higher death rates for opioid drug overdose.
According to the Centers for Disease Control and Prevention, in 2017 there were 70,237 drug overdose deaths, and opioids were responsible for ~68% (or 47,600) of those deaths. In addition, the data shows that the states with the highest rates of drug overdose was West Virginia and Ohio with 57.8 per 100,000 deaths and 46.3 per 100,000 deaths, respectively. There have been recent efforts to combat this on the local, state, and federal levels. In the state of West Virginia, for example, recent laws such as the Opioid Reduction Act of 2018 substantially decreased the amount of opioids that can be prescribed at a time -- only a 4 day supply maximum. In addition, government healthcare programs such as Medicaid provide the resources necessary to combat opioid drug abuse and addiction. Ohio has also had similar efforts, such as their recently implemented RecoveryOhio Initiative where a council comprised of experts in the medical, pharmaceutical, legal, and sociological fields has been created to implement and promote efforts for safe legal opioid use. In addition, Ohio has implemented the Naloxone initiative. Naloxone is a drug that reverses the effects of a drug overdose. Therefore, the government has provided additional funding for emergency medical services to have the drug on hand when responding to emergency situations.
(ii) Importance and Goals of the Project
While our broad goal is to analyze the Opioid Crisis in the United States, we specifically wish to understand the factors that lead to a higher death rate and to understand the opioid crisis from the supply chain perspective. This is a new perspective, because traditionally, analysis has focused on the addicts (e.g. their race, income, and age group). We think an analysis from a supply chain direction will provide new insights that could be useful in combating the crisis. Recently, for example, lawsuits involving Purdue Pharma and other manufacturers has highlighted the possible role of distributors in trying to overprescribe opioids for profit. We wanted to continue investigating whether more prescriptions, higher doses, number of manufacturers/distributors, number of pharmacies, or market share of these distributors are related to death rates in each county. This could inspire possible policies to tackle the crisis (e.g. cap market share, reduce the number of distributors or pharmacies). On a broader view, the end goal would be to expand our analyses to other regions of the United States, even the areas that have lower rates of drug overdose deaths.
 1

### (iii) Roadmap and Methodology
In order to achieve our analysis goals, we first worked to collect data on opioid drugs and related quantities such as their manufacturers and pharmacies, death rates and other additional variables such political affiliation and income for each of the counties in the state of interest.
We then decided our response and predictors. Our predictors are the supply-chain related data such as active weight of drugs transacted, and we wanted our response to be drug-induced mortality rates. This is the most commonly used metric by other studies on the opioid crisis, more readily available than other possible measures like county budget spent on rehabilitation services, and most easily understood by policymakers.
With the datasets cleaned and grouped on county level, we will use the information of each said county to then create models. Before that, we will do Exploratory Data Analysis (EDA) in order to make appropriate transformations to our data and figure out which interactions are significant to our response (death rates). Following the EDA, we will begin to explore various models in order to fit our response. Before we began to create our models, we make a 75-25 train-test split for training and validation.
Once all models have been fit, we will proceed to calculate AIC’s and determine our final model, with which we will do inference and recommend policy implementations. Our primary goal is inference, and therefore we will keep in mind the trade-off between inference power and predictive performance, while also paying close attention to interactions, to make recommendations are very specific, and only have important variables because policy makers don’t have an infinite amount of resources to tackle every single related variable/aspect.

## Data and EDA

### (i) Data sources
We have two main data sources. The first source is a database by The Washington Post. This database, ​ARCOS,​ contains information on oxycodone and hydrocodone pills, which are three-quarters of all painkillers transactions between manufacturers and pharmacies between 2006 and 2012 for all the states. The second main source is the United States’ Centers for Disease Control and Prevention website that provides data on disease and mortality rates for all states in a given year (dataset referred to as ​DPM​). From both sources, we obtained information on opioid transactions and estimated drug mortality death rates related to drug poisoning for West Virginia and Ohio. This gave us two datasets: ARCOS, for opioids transactions, and DPM, for age-adjusted death rates and other related variables. We also obtained additional data on household median income and political affiliation for each county in West Virginia and Ohio. The data are from the United States Census Bureau and New York Times respectively.

### (ii) Data wrangling and variables generation

Because the intended reader for our analysis is a policymaker, we will probably be interested in data for the most recent data available, which is 2012; we also imagine ourselves making policy recommendations to local governments, so we did the analysis at the county level. After obtaining all the datasets of interest, we merged all the data together. The resultant dataset contains observations for 2012, summarized by county. In the original merged dataset, each row was a single transaction. Finally, we used the available variables to create additional variables that will summarize information about suppliers and distributors on a county-level. Below is a description of each of the variable in the final dataset:
1. est_death_rate_cat: ​binned estimated age-adjusted drug overdose death rate for a given county (ordinal factor with levels ​cat_1​, ​cat_2​, ... , ​cat_6​).
2. BUYER_COUNTY: ​county of opioid buyer.
3. active_wt_person_county: ​total active drug weight in grams per ten thousand people for
a given county (numeric).
4. perc_oxy: ​percentage of total active drug weight that is oxycodone for a given county
(numeric).
5. pharmacy_num_ptt: number of pharmacies per ten thousand people for a given county
(numeric).
6. most_dist_channel: ​the most common channel of drug distribution for a given county
(nominal factor with two levels, ​CHAIN PHARMACY​ or ​RETAIL PHARMACY​).
7. dominance: ​market dominance (nominal factor, ​Yes ​if the top two distributors account for more than 50% of total active weight of drugs transacted in a county, and ​No
otherwise).
8. median_income​: ​median household income (numeric).
9. politcal_aff: predominant political party in the county (nominal factor with two levels,
Repulican​ or ​Democrat​).
10. distr_num_ptt: ​number of unique distributors that supply drugs to a given county per ten
thousand people (numeric).
est_death_rate_cat ​is our response variable. ​cat_1 ​corresponds to 2 to 7.9 deaths, ​cat_2
is 8 to 13.9 deaths, ​cat_3 ​is 14 to 19.9 deaths, ​cat_4 ​is 20 to 25.9 deaths, ​cat_5 ​is 26 to 29.9 deaths, and ​cat_6 ​represents 30+ deaths. These rates are per hundred thousand, age adjusted by 2000 U.S. Census. The rest of the variables are predictors.
We couldn’t work with the full ARCOS dataset and do analysis for the entire country because the ARCOS dataset is too huge - over 300 Gigabytes. The subset of ARCOS for West Virginia and Ohio combined is already over 4 gigabytes, and since these two counties have suffered the most deaths in the opioid crisis, they are a good subset to investigate.
The total number of observations in our final dataset is 143, representing 55 counties from West Virginia and 88 for Ohio. There are no missing data. However, given the small size of the dataset, our analysis and inference might be limited.
