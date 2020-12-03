# vacdist
An Exploration of Genetic Algorithm using DEAP: COVID-19 Vaccine Distribution

This is an exercise in exploration of Genetic Algorithm as part of ECE 788: Computational Intelligence class with Prof. Qing Gary Liu at New Jersey Institute of Technology.

## Problem Evolution
- Inspired by “Locating an Emergency Response Unit” problem from the Haupt & Haupt, “Practical Genetic Algorithms” 2004.
- Started off with Optimization of resource distribution in an epidemic/pandemic/emergencies in Myanmar.
- For pragmatic data access, it switched from Myanmar to United States.
- Problem got more specific: COVID-19  Vaccine Distribution

## Problem Definition
If we have 1 million vaccine doses available and we have to distribute the vaccine by county (with average country population of 100,000), then, we have to find 10 counties with the highest risk to send the vaccines to. In order to do so, we want to find maxima value of the 10 counties in question.

### Fitness Function
The concept is based on Patel, et al., set up of the optimization problem for finding optimal vaccination strategies for pandemic influenza using genetic algorithm.

<img src="https://latex.codecogs.com/svg.latex?\Large&space;\sum_{i=1}^{g}n_iR_t(c+1)(h+1)d_i" title="\Large \sum_{i=1}^{g}n_iR_t(c+1)(h+1)d_i" />
where:

- g = number of age groups from Census Bureau
- n_i = population size of each age group
- R_t = infection rate OR reproductive number
- c = index of case density in the county risk factor
- h = index of ICU headroom risk factor
- d_i = likelihood of death for each age group (modified to fit CDC age bins)

This problem is not representative of the real-world considerations with regards to vaccine distribution for COVID-19. This is an exercise for an optimization problem in Genetic Algorithms.
My goal here is to create a value for each county that represents the relative risk level. This achieves my goal and all I have to do with Genetic Algorithm is simply solving for a sum of relative risk level for a set of 10 counties that are or close to the global maxima.

#### Census Bureau Age Groups
Using Census Bureau’s 2019 population estimates for all the counties in 50 states + District of Columbia. Data has bins for age groups with 5-year intervals, the last bin being 85+ year olds

#### Infection Rate (CovidActNow)
- Infection rate is the estimated number of new people each COVID-positive person will infect. Also known as “R(t),” the infection rate tells us how fast COVID is spreading in a specific area, at a specific time. For example, an infection growth rate of 3 means that one case of COVID would produce three new secondary cases.
- This value can change over time, depending on behavior and interventions within a community. Infection rate is different from the total number of cases because a place with a smaller population and few total cases can still have a very high infection rate, meaning that one person could go on to infect many people.
- The higher the R(t) is in a given place, the faster COVID is spreading there; an R(t) above 1 means that the number of cases is growing, while an R(t) below 1 means that cases are shrinking. Covid Act Now uses R(t), or infection rate, as one of its main metrics for COVID risk assessment.
- The value range from 0 and above.

#### Case Density (CovidActNow)
- Case density is a measure of the number of COVID cases divided by the number of people (per capita) in a given community. Case density can refer to new cases over, say, the last 14 days or it can refer to total cases since the onset of the pandemic. The word “density” means that the case counts are expressed as a per-capita value.
- Case density risk levels are represented with the range of 0 to 4.

#### ICU Headroom (CovidActNow)
- ICU headroom used is the percentage of ICU beds available for COVID patients, after excluding those already in use by non-COVID patients. It is used to measure an Intensive Care Unit’s (ICU) ability to handle a surge of patients in the case of a COVID outbreak. The ICU is the part of a hospital reserved for people who are very sick. Each bed is equipped with special equipment that regular hospital beds don’t have, and usually have one or two dedicated nurses to monitor each patient.
- ICU headroom used is one of Covid Act Now’s main metrics for understanding COVID, as measuring and predicting ICU capacity gives a picture of whether hospitals can handle an increased load of new COVID cases without having to resort to crisis standards of care.
- ICU Headroom risk levels are represented with the range of 0 to 4.

#### Death Likelihood by Age (CDC)
Since, CDC’s age bins do not exactly match up with Census Bureau’s, I changed 5-17 years bin to 5 - 19 years and 18 - 29 years to 20 - 29 years.
![Image of CDC_Death_Likelihood_By_Age](https://www.cdc.gov/coronavirus/2019-ncov/images/covid-data/hospitalization-death-by-age-lg.jpg)

### DEAP eaSimple
DEAP used Chapter 7 (Introduction to evolutionary algorithms) of Back, Fogel and Michalewicz, “Evolutionary Computation 1 : Basic Algorithms and Operators”, 2000.
It’s based on Genetic algorithms, Evolution strategies, and Evolutionary programming methods.
You can tweak the parameters to use it as Simple Genetic Algorith, which is how I have used it for this project.

#### eaSimple Pseudocode
![Image of esSimple Pseudocode](./img/eaSimple.png)
where:
- µ (mu): parent
- λ (lamda): child
- Θ_ι (theta_iota): termination criterion
- Θ_r (theta_r): recombination/crossover criterion
- Θ_m (theta_m): mutation criterion
- Θ_s (theta_s): selection criterion

## Findings
### SGA Parameters (Population)
Started with population parameters based on Alajmi & Wright's finding showing it has higher impact on fitness than other parameters. Each set has 3 runs using the same DEAP settings. We have a known optimal solution which is 1.1e+10.
|  | Set 1 | Set 2 | Set 3 | Set 4 | Set 5 | Set 6 | Set 7 |
| --------------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
| Genes | 10 | 10 | 10 | 10 | 10 | 10 |
| Population | 5 | 15 | 30 | 50 | 100 | 200 | 300 |
| crossover | 1-pt (0.75) | 1-pt (0.75) | 1-pt (0.75) | 1-pt (0.75) | 1-pt (0.75) | 1-pt (0.75) | 1-pt (0.75) |
| Mutation | Uni (0.1) | Uni (0.1) | Uni (0.1) | Uni (0.1) | Uni (0.1) | Uni (0.1) | Uni (0.1) |
| Selection | Roulette | Roulette | Roulette | Roulette | Roulette | Roulette | Roulette |
| Generations | 500 | 500 | 500 | 500 | 500 | 150 | 100 |
| HallOfFame | None | None | None | None | None | None | None |
| CPU Time (Sec) | 68 | 242 | 508 | 843 | 1693 | 1022 | 1020 |
| Best Solution | 9.2e+08 | 2.9e+09 | 2.8e+09 | 5.0e+09 | 6.0e+09 | 8.9e+09 | 9.1e+09 |
| Converge Gen | 9 | 4 | 24 | 37 | 27 | 55 | 79 |

![Image of Set 7 Run 2](./img/Figure_07b.png)
The above image shows the stats from the Set 7 Run 2.
Here, we can observe from the table that the best solution has a positive correlation to the change in population. The same can be said about the CPU time as well. Below are the counties as well as their risk rank that was generated as the best solution on Set 7 Run 2.
- Broward County ,  Florida (4th)
- Ocean County ,  New Jersey (8th)
- Hudson County ,  New Jersey (34th)
- Hampden County ,  Massachusetts (42nd)
- Clark County ,  Nevada (1st)
- Maricopa County ,  Arizona (9th)
- Tarrant County ,  Texas (17th)
- Essex County ,  Massachusetts (10th)
- Middlesex County ,  Massachusetts (13th)
- Cook County ,  Illinois (11th)
As we can see, the list includes 5 FIPS areas with the top 10 worst risk, from all the FIPS areas in 50 states as well as District of Columbia, with exception of areas with NaN rows from CovidActNow. This is an expected result with Genetic Algorithms. GA doesn't produce the perfect optimal solution. But it does however get very close to the optimal solution.