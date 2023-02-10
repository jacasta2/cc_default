# Exploratory Data Analysis

I ran a basic EDA that (i) checks the distribution of some variables and performs variable transformations (log, square root and cubic root) in case of non-normal behaviors and (ii) explores the relationship between some variables and defaulting. Whether a credit holder defaults is captured by the feature `DEFAULT`,[^1] where 0 is a non-defaulter and 1 a defaulter. The notebook may take some time to run due to the plotting of some of the histograms. I summarize the main results below and you can check all supporting visualizations in the notebook.
[^1]: `DEFAULT` is `default.payment.next.month` in the original dataset. I use `DEFAULT` for simplicity. 

## `LIMIT_BAL`

This continuous variable captures the credit amount. Its histogram shows non-normal behavior (a right-skewed distribution). A visual inspection of the transformations suggests the square root transformation is somewhat better than the other two.

A boxplot of `LIMIT_BAL` contrasting defaulters and non-defaulters suggests the credit amount might be an important feature to understand defaulting, where smaller credit amounts lead to more defaulting. A further analysis that discretizes the credit amount into several categories shows the probability of defaulting reduces as the credit amount increases.

## `BILL_AMT#`

These continuous variables capture the amounts to be paid shown in bill statements. They're available for six months, where `BILL_AMT1` captures the amount to be paid in the Sep statement, `BILL_AMT2` captures the amount to be paid in the Aug statement, and so on up to `BILL_AMT6` (Apr).

Their histograms show non-normal behaviors (right-skewed distributions). These values are positive balances for the credit holder. A visual inspection of the transformations suggests the cubic root transformation is better than the other two.

Boxplots of `BILL_AMT#` contrasting defaulters and non-defaulters suggest the amounts to be paid shown in bill statements might not be important features to understand defaulting.

A correlation analysis shows strong correlations among the `BILL_AMT#` features. This suggests we could only use one of them in the modeling work. The last amount to be paid (Sep) seems a straightforward choice. A further analysis that discretizes this amount into several categories shows the probability of defaulting:

- is null for negative bill amounts (these represent positive balances for the credit holder);
- remains fairly constant (at around 20%) for bill amounts up to 455,970;
- shows two jumps: one for bill amounts up to 512,474 (with defaulting at around 30%) and another one for bill amounts up to 625,483 (with defaulting at around 40%);
- and comes back to 0 for bill amounts greater than 625,483.

Contrary to the visual inspection of boxplots mentioned above, this last analysis suggests it might be worthwhile to look at the amount to be paid in the last available statement to understand defaulting.

## `PAY_AMT#`

These continuous variables capture the paid amounts. They're available for six months, where `PAY_AMT1` captures the amount paid in Sep, `PAY_AMT2` captures the amount paid in Aug, and so on up to `PAY_AMT6` (Apr).

Their histograms show non-normal behaviors (right-skewed distributions). A visual inspection of the transformations suggests the log transformation is better than the other two.

Boxplots of `PAY_AMT#` contrasting defaulters and non-defaulters suggest the paid amounts might be important features to understand defaulting.

A correlation analysis shows some mild correlations among some of the `PAY_AMT#` features. Similar to the analysis ran for `BILL_AMT#`, we'll discretize the last payment made (Sep) into several categories. This analysis shows the probability of defaulting:

- is around 20% for small payments (up to 43,677);
- except for two bands, it remains fairly constant (at about 10%) for values up to 305,743;
- and becomes 0% for values greater than 305,743.

This last analysis reinforces that it might be worthwhile to look at  the paid amounts to understand defaulting.

A quick inspection of several records suggests `PAY_AMT1` is the payment made for the Aug bill or `BILL_AMT2`, `PAY_AMT2` is the payment made for the Jul bill or `BILL_AMT3`, and so on. A correlation analysis suggests these variables are redundant since they're strongly correlated with `BILL_AMT1`.

## `PAY_#`

These categorical variables capture the payment statuses. They're available for six months, where `PAY_1` captures the payment status in Sep, `PAY_2` captures the payment status in Aug, and so on up to `PAY_6` (Apr).[^2]
[^2]: `PAY_1` is `PAY_0` in the original dataset. I use `PAY_1` to keep consistency with the naming convention of `BILL_AMT#` and `PAY_AMT#`.

Barplots of default probability contrasting the payment statuses suggest these statuses might be important features to understand defaulting, where defaulting increases when there're payment delays. However, note a 1-month delay has contrasting effects according to what month we look at.

## `AGE`

This continuous variable captures the age of the credit holder. Its histogram shows a somewhat non-normal behavior (right-skewed). A visual inspection of the transformations suggests neither of the them are successful.

A boxplot of `AGE` contrasting defaulters and non-defaulters suggests the credit holder's age might not be an important feature to understand defaulting.

## `SEX`

This categorical variable captures the reported gender of the credit holder. A barplot of default probability contrasting males and females and a boxplot of the credit amount contrasting males and females suggest the credit holder's gender might be a somewhat important feature to understand defaulting, where males seem to default more.

## `EDUCATION`

This categorical variable captures the education level of the credit holder. A barplot of default probability contrasting the education levels and a boxplot of the credit amount contrasting the education levels suggest the credit holder's education level might be an important feature to understand defaulting, where people with graduate education and other/unknown education levels seem to default less.

## `MARRIAGE`

This categorical variable captures the marital status of the credit holder. A barplot of default probability contrasting the marital statuses and a boxplot of the credit amount contrasting the marital statuses suggest the credit holder's marital status might be an important feature to understand defaulting, where some statuses (single and other) seem to default less.
