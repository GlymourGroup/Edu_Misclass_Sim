# Edu_Misclass_Sim
simulation to estimate bias from education misclassification, by Maria Glymour

# Code
```
clear
set seed 3423
set obs 100000
gen id=_n
gen reduc=runiform() /*random number used to generate education variable*/
gen educ=(reduc>.8)+(reduc>.7)+(reduc>.5)
gen lnoddsdeath=-4.1+rnormal()-educ*.3 /*log odds death a function of education*/
gen dead=runiform()<(exp(lnoddsdeath)/(1+exp(lnoddsdeath)))

/* make a new version of education with misclassification*/
gen educ2=educ
gen reduc2=runiform()
replace educ2=1 if (dead==1 & educ==0 & reduc2>.9)
replace educ2=3 if (dead==1 & educ==2 & reduc2>.72)
gen educgt0=educ>0
gen educ2gt0=educ2>0

/*compare logistics predicting death with original vs misclassified education*/
logit dead i.educ, or
logit dead i.educ2, or

logit dead educgt0, or
logit dead educ2gt0, or

/*generate a copy of the dead people to mirror the death certificate-ACS merged design*/
expand 2 if dead==1 , gen(deathcert)
replace dead=0 if deathcert==0
gen educ3=educ if dead==0
replace educ3=educ2 if dead==1
gen educ3gt0=educ3>0
sum
logit dead i.educ, or
logit dead i.educ3, or
logit dead educgt0, or
logit dead educ3gt0, or
```
