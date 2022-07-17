---
title: "Decision and Classification Trees"
enableToc: true
---

- Decision Tree makes a statement, then makes a statement based on the truth of the statement
- ## Classification Tree
- classifies things into categories
	- If True, go left <- convention 
	- top tree is root node
	- leaves have nodes pointing towards them, none leaving.
	- else its a branch
- **Impure:** Leaves that contain true and false outcomes 
- Gini Impurity for leaf: $$=1-(\text{P(Yes)})^2-(\text{P(No)})^2$$
- Total **Gini Impurity** = weighted average of Gini Impurities for leaves
	- weight of leaf = number of ppl in leaf / all ppl in other leafs
- predictor with lowest gini impurity is chosen as root node
## Regression Tree 
- predicts numeric values 
- split data into two groups by finding the threshold that gives the smallest sum of squared residuals
- typically we set a minimum number of observations to allow for a split
	- common threshold is 20
	- i.e. leaves can have most 20 observations 
- Calculate sum of squared residuals for all predictors, candidate with lowest value is root node

## Pruning Regression Tree
- Cost Complexity Pruning 
- Calculate sum of squared residuals for all possible subtrees 
	- calculate SSR for all leaves and sum 
- Tree score = $SSR +\alpha T$
	- T = number of leaves
- To find $\alpha$, we increase it until pruning leaves increases tree score 


## AdaBoost

Amount of Say = $\frac{1}{2}\log{\left(\frac{1-\text{Total Error}}{\text{Total Error}}\right)}$
