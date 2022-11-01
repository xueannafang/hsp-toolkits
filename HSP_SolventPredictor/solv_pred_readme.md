Last update on 01/11/2022

# Solvent Predictor


Given that HSP of a solvent mixture follows a linear combination of each individual component, researchers can easily calculate the HSP of solvent systems with known components. It is however much more difficult to reverse this process.

<p>
 <img src="https://github.com/xueannafang/hsp-toolkits/blob/main/figs/bg_solvpred.png" width=1000>
 </p>

The aim of *Solvent Predictor* is to support the solvent suggestion when a desired goal of HSP is known.

The key function is to convert the target HSP into a multi-solvent list based on the requirement of user.

### Set up

- Download the folder of **HSP_SolventPredictor** to local working directory on Windows.
- Open **Solv_pred_class.ipynb** using Jupyter Notebook.

*Please note: **HSP_SolvP.py** contains key calculation process for *Solvent Predictor*.*

*It is imported at the first step. Please be careful to change its name.*
 
### Run *Solvent Predictor*

**Step 1 Load all the related packages:**

Execute the first cell
```
import numpy as np
import pandas as pd
from scipy.linalg import pinv
import itertools
import abc
import HSP_SolvP as HSP
import os
```

**Step 2 Prepare input spreadsheets using Microsoft Excel:**

- **solvent candidates** (input_solv_sel.xlsx)

 This is the solvent pool for users to choose from.
 
 Solvents appear on this list will be considered as candidates of multi-solvent systems.

 <p>
  <img src="https://github.com/xueannafang/hsp-toolkits/blob/main/figs/input_solv_sel_example.png">
 </p> 


   Users can edit this form depending on their preference.
   
   *To remove an entry:*
   
   - Select the whole row -> right click -> delete.
   - Update the number in "Group" column by dragging the first two cells to the last filled cell with "filled series" option.
   <p>
 <img src="https://github.com/xueannafang/hsp-toolkits/blob/main/figs/to_rm_from_pool.png">
 </p>
 
   *Note: The "Group" number must be continuous integers starting from 1 to the total number of solvents.*
   
   *Empty cell, discontinuous or overflowed numbers, or wrong sequence in the "Group" column will lead to error.*
   
   <p>
   <img src="https://github.com/xueannafang/hsp-toolkits/blob/main/figs/ip_solv_sel_cm_er.png" width=500>
   </p>
   
   *To add an entry*
   
   - First search the CAS No. of the solvent in the database (db.xlsx).
   - Search this CAS No. in the solvent candidate list (input_solv_sel.xlsx).
   
    - If it is found:
   
   The solvent you are looking for is already on the list.
   
   **Do not add it again.** (Repeated entries will cause crash.)
   
    - If it is not found:
   
   Copy the first three cells (No., CAS, Name) in the database (db) spreadsheet to the corresponding cells (No_db, CAS, Solvent) in the candidate list.
   
          (left: db.xlsx) -> (right: input_solv_sel.xlsx)
   
          No. -> No_db
   
          CAS -> CAS
   
          Name -> Solvent
   
   Note that the "No_db" column is not a must to be filled, but is still recommended to do so. (*See appendices for explanation.*) Removing this whole column will fail the calculation.
   
   - Then update the "Group" column.
   
   <p>
   <img src="https://github.com/xueannafang/hsp-toolkits/blob/main/figs/to_add_to_pool.png" width=1000>
   </p>
   
- **Database** (db.xlsx)

This database is adapted from C. M. Hansen, Hansen solubility parameters (HSP), Second edn, 2011, vol. 118.

D, P, H stands for dispersion, dipolar and hydrogen bond sub-parameters (usually written as delta_D, delta_P, detla_H). The unit is MPa1/2

Users can add alias/abbreviations for solvents with long names, e.g., acetonitrile -> ACN, in the "alias" column.

*Note that in this beginner-friendly version, content not in the first six columns (No., CAS, Name, D, P, H) will not affect the calclation. Only to make it convenient for users to search the request entries.*

*Experienced users can personalise the additional parameters by operating other columns of this database file and include them in DataFrame settings. (See FAQs for more discussion.)*


**Step 3**

Specify arguments in **mix_pred()** function:


**n**
    
- maximum number of solvents in each multi-solvent combination
- default = 2
- positive integer.

**target HSP**

- contains three parameters in the order of delta_D, delta_P, delta_H
- Each of them must be a float.

**rep_time**

- repeated calculation times for perturbation applied on the target HSP matrix (*See Principles for details*)
- default = 50
- positive integer

**std**

- threshold of standard deviation of perturbation (*See Principles for details*)
- default = 0.1
- Standard deviation larger than this value will be filtered.

**tol_pred**

- tolerance of concentration deviated from the target (*See Principles for details*)
- Calculated concentration deviate more than this value will be filtered.

**red_tol**

- tolerance of redundant solvent concentration (*See Principles for details*)
- Calculated conecentration below than this value will be filtered.


**Step 4**

Upload solvent candidate list (input_solv_sel.xlsx) and database (db.xlsx) into **SolvPred()** and execute the second cell:

```
class SolvPred():
    
    def __init__(self, input_solv, db):
        self.pred = HSP.SolvPredictor(input_solv, db)

    def mix_pred(self, n = 2, rep_time = 50, std = 0.1, tol_pred = 1, red_tol = 0.01):
        self.pred.run_all(n, 18.0, 1.4, 2.0, rep_time = rep_time, std = std, tol = tol_pred, red_tol = red_tol)

sp = SolvPred(r'input_solv_sel.xlsx', r'db.xlsx')
sp.mix_pred()
```




## Principles

perturbation on target HSP




## FAQs

1. What is No_db for?

This is because the style of "CAS No." is text. It is just an intermediate variable to fetch the HSP data. *Solvent Predictor* will first read the input CAS No. from the solvent candidate list, and then use the CAS info to fetch the "No." of the corresponding solvent in the database. The rest of calculation is only relied on the "No." to improve the efficiency. In the output spreadsheet, the solvent name will


2. How to include more parameters in the database?






