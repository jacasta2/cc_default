# Modeling

I first perform some data processing where I transform, scale and one-hot encode several features. Then, I train, validate and test a logistic regression model and a XGBoost classifier. I also do some manual hyperparameter tuning to address overfitting in the XGBoost classifier.

I proceed with a k-fold cross-validation using the models default implementation and compare their performance. Finally, I perform a hyperparameter tuning using k-fold cross-validation and compare the performance of the resulting models using a validation set.

I summarize the main results below and you can check all supporting work and comments in the notebook.

I believe it's important to mention that without a baseline level of performance it's difficult to assess whether a model is underfitting. Thus, we mainly address overfitting.

## Train/validation/test sets approach

### Logistic regression

The training and validation accuracy of the logistic regression are 0.8224 and 0.8168, respectively, which suggests the model isn't overfitting the training data.

### XGBoost classifier

The training and validation accuracy of the XGBoost classifier are 0.8895 and 0.8127, respectively, which suggests the model is overfitting the training data.

We manually explore changes to some hyperparameters, observing that some of these changes are able to reduce overfitting. For example, reducing the maximum depth of a tree and shrinking the feature weights help reduce overfitting, bringing the training accuracy closer to the validation accuracy.

We also explore a k-fold cross-validation with the default implementation of the XGBoost classifier, finding that this process significantly reduces overfitting.

### Model comparison

The confusion matrices are shown below.

<div id="image-table">
    <table>
	    <tr>
    	    <td style="padding:10px">
        	    <img src="cm_log-reg.png" width="300"/>
      	    </td>
            <td style="padding:10px">
            	<img src="cm_xgboost.png" width="300"/>
            </td>
        </tr>
    </table>
</div>

Some overall performance metrics are shown next.

| Model                     | Accuracy  | Precision | Recall    | F1 score  | ROC AUC   | Generalization error  |
| ---                       | ---       | ---       | ---       | ---       | ---       | ---                   |
| Logistic regression       | 0.8168    | 0.6597    | 0.3549    | 0.4615    | 0.6515    | 0.1762                |
| Cross-validated XGBoost   | 0.8126    | 0.6389    | 0.3517    | 0.4537    | 0.6476    | 0.1847                |

Comparing both models, we can conclude they perform virtually the same. However, although the accuracy seems relatively high, the confusion matrices and their metrics tell us both models are rather poor. In particular, the recall scores indicate both of them have a high rate of false negatives, i.e., the rate at which the models predict people as non-defaulters when in fact they did default is high. Moreover, the ROC AUCs indicate the models have a 65% chance of distinguishing between positive and negative classes, which doesn't sound really promising.

## Model comparison using cross-validation

We take the analysis one step further and apply k-fold cross-validation to both models and compare them. The performance metrics are shown next.

| Metric    | Logistic regression   | XGBoost classifier    |
| ---       | ---                   | ---                   |
| Accuracy  | 0.8208                | 0.8126                |   
| Precision | 0.6801                | 0.6388                |  
| Recall    | 0.3583                | 0.3517                |  
| F1 score  | 0.4692                | 0.4536                |
| ROC AUC   | 0.7722                | 0.7606                |

Overall, both models perform in a similar way and rather poorly. In particular, the recall scores indicate both of them have a high rate of false negatives. Perhaps the only important difference is in precision, where the logistic regression outperforms the XGBoost classifier. A view of how these metrics behave during the cross-validation procedure shows these results are consistent across folds (check this out in the notebook).

Note the rather big increases in ROC AUCs compared to the ones from the section above. The former are computed using the method `cross_val_score()`. If instead we make predictions using the method `cross_val_predict()` (as we did to compute the scores of the cross-validated XGBoost from the section above) and then compute the ROC AUCs, we would get similar ROC AUCs to the ones from the section above. It seems the differences are the result of whether we're training or making predictions. However, the differences are still rather puzzling since the other scores remain fairly the same. I captured this in a [Question I posted myself in Stack Overflow](https://stackoverflow.com/questions/75622027/why-a-roc-auc-score-from-a-regular-cross-validation-is-very-different-from-a-roc) hoping to get help to understand what could be going on here. 

## Model comparison after hyperparameter tuning

Finally, we fine-tune some hyperparameters of both models using cross-validation, validate them using a validation set and compare their performance. The performance metrics are shown next.

| Metric    | Logistic regression   | XGBoost classifier    |
| ---       | ---                   | ---                   |
| Accuracy  | 0.8200                | 0.8173                |   
| Precision | 0.6787                | 0.6657                |  
| Recall    | 0.3534                | 0.3497                |  
| F1 score  | 0.4648                | 0.4585                |
| ROC AUC   | 0.6530                | 0.6499                |

Overall, both models perform in a similar way and rather poorly. In particular, the recall scores indicate both of them have a high rate of false negatives. Differences across the performance metrics between the models are pretty consistent, with the logistic regression slightly ourperforming the XGBoost classifier.
