# Optimization: The Data-Driven Diet



The "diet problem" is a classic optimization problem in operations research. Back in the 1930s and 1940s, the United States Army wanted to know what to feed its soldiers in order to satisfy healthy nutritional requirements at minimum cost. This research became one of the first large-scale optimization problems to be studied in practice.

The diet problem highlights the key aspects of optimization: reducing waste, minimizing cost, and meeting essential constraints. In this analysis, we will revisit the diet problem by applying an optimization model to a modern nutritional dataset. Our goal will be to find the cheapest diet possible that satisfies the maximum and minimum daily nutrional constraints, using the food items that are listed in the dataset. 

## Exploring the Data

We begin the analysis with an initial exploration of the data. 


```python
import pandas as pd
import pulp 

file_path = r"E:\ISYE 6501\Week 11\HW11\data 15.2\diet.xls"

# Read the Excel file
data = pd.read_excel(file_path)

display(data)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Foods</th>
      <th>Price/ Serving</th>
      <th>Serving Size</th>
      <th>Calories</th>
      <th>Cholesterol mg</th>
      <th>Total_Fat g</th>
      <th>Sodium mg</th>
      <th>Carbohydrates g</th>
      <th>Dietary_Fiber g</th>
      <th>Protein g</th>
      <th>Vit_A IU</th>
      <th>Vit_C IU</th>
      <th>Calcium mg</th>
      <th>Iron mg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Frozen Broccoli</td>
      <td>0.16</td>
      <td>10 Oz Pkg</td>
      <td>73.8</td>
      <td>0.0</td>
      <td>0.8</td>
      <td>68.2</td>
      <td>13.6</td>
      <td>8.5</td>
      <td>8.0</td>
      <td>5867.4</td>
      <td>160.2</td>
      <td>159.0</td>
      <td>2.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Carrots,Raw</td>
      <td>0.07</td>
      <td>1/2 Cup Shredded</td>
      <td>23.7</td>
      <td>0.0</td>
      <td>0.1</td>
      <td>19.2</td>
      <td>5.6</td>
      <td>1.6</td>
      <td>0.6</td>
      <td>15471.0</td>
      <td>5.1</td>
      <td>14.9</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Celery, Raw</td>
      <td>0.04</td>
      <td>1 Stalk</td>
      <td>6.4</td>
      <td>0.0</td>
      <td>0.1</td>
      <td>34.8</td>
      <td>1.5</td>
      <td>0.7</td>
      <td>0.3</td>
      <td>53.6</td>
      <td>2.8</td>
      <td>16.0</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Frozen Corn</td>
      <td>0.18</td>
      <td>1/2 Cup</td>
      <td>72.2</td>
      <td>0.0</td>
      <td>0.6</td>
      <td>2.5</td>
      <td>17.1</td>
      <td>2.0</td>
      <td>2.5</td>
      <td>106.6</td>
      <td>5.2</td>
      <td>3.3</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Lettuce,Iceberg,Raw</td>
      <td>0.02</td>
      <td>1 Leaf</td>
      <td>2.6</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.8</td>
      <td>0.4</td>
      <td>0.3</td>
      <td>0.2</td>
      <td>66.0</td>
      <td>0.8</td>
      <td>3.8</td>
      <td>0.1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Crm Mshrm Soup,W/Mlk</td>
      <td>0.65</td>
      <td>1 C (8 Fl Oz)</td>
      <td>203.4</td>
      <td>19.8</td>
      <td>13.6</td>
      <td>1076.3</td>
      <td>15.0</td>
      <td>0.5</td>
      <td>6.1</td>
      <td>153.8</td>
      <td>2.2</td>
      <td>178.6</td>
      <td>0.6</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Beanbacn Soup,W/Watr</td>
      <td>0.67</td>
      <td>1 C (8 Fl Oz)</td>
      <td>172.0</td>
      <td>2.5</td>
      <td>5.9</td>
      <td>951.3</td>
      <td>22.8</td>
      <td>8.6</td>
      <td>7.9</td>
      <td>888.0</td>
      <td>1.5</td>
      <td>81.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>64</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>65</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>Minimum daily intake</td>
      <td>1500.0</td>
      <td>30.0</td>
      <td>20.0</td>
      <td>800.0</td>
      <td>130.0</td>
      <td>125.0</td>
      <td>60.0</td>
      <td>1000.0</td>
      <td>400.0</td>
      <td>700.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>66</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>Maximum daily intake</td>
      <td>2500.0</td>
      <td>240.0</td>
      <td>70.0</td>
      <td>2000.0</td>
      <td>450.0</td>
      <td>250.0</td>
      <td>100.0</td>
      <td>10000.0</td>
      <td>5000.0</td>
      <td>1500.0</td>
      <td>40.0</td>
    </tr>
  </tbody>
</table>
<p>67 rows × 14 columns</p>
</div>


Upon inspecting our dataset, we can see that the dataset contains 66 rows and 14 columns. Rows 1-63 contains the various food options that are available and the price/serving/nutritional information. Row 64 is a blank space and rows 65-66 are the minimum/maximum daily intake constraints for each nutrient. Before moving forward with the analysis, we will separate the food data and the nutrient constraints. 


```python
# Select the first 63 rows of data, which contain the food items and their attributes 
food_data = data[:64]

# Isolate rows starting from row 65 and onward, and all columns starting from column 3
nutrient_constraints = data.iloc[65:, 3:].reset_index(drop=True)
display(food_data)
display(nutrient_constraints)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Foods</th>
      <th>Price/ Serving</th>
      <th>Serving Size</th>
      <th>Calories</th>
      <th>Cholesterol mg</th>
      <th>Total_Fat g</th>
      <th>Sodium mg</th>
      <th>Carbohydrates g</th>
      <th>Dietary_Fiber g</th>
      <th>Protein g</th>
      <th>Vit_A IU</th>
      <th>Vit_C IU</th>
      <th>Calcium mg</th>
      <th>Iron mg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Frozen Broccoli</td>
      <td>0.16</td>
      <td>10 Oz Pkg</td>
      <td>73.8</td>
      <td>0.0</td>
      <td>0.8</td>
      <td>68.2</td>
      <td>13.6</td>
      <td>8.5</td>
      <td>8.0</td>
      <td>5867.4</td>
      <td>160.2</td>
      <td>159.0</td>
      <td>2.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Carrots,Raw</td>
      <td>0.07</td>
      <td>1/2 Cup Shredded</td>
      <td>23.7</td>
      <td>0.0</td>
      <td>0.1</td>
      <td>19.2</td>
      <td>5.6</td>
      <td>1.6</td>
      <td>0.6</td>
      <td>15471.0</td>
      <td>5.1</td>
      <td>14.9</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Celery, Raw</td>
      <td>0.04</td>
      <td>1 Stalk</td>
      <td>6.4</td>
      <td>0.0</td>
      <td>0.1</td>
      <td>34.8</td>
      <td>1.5</td>
      <td>0.7</td>
      <td>0.3</td>
      <td>53.6</td>
      <td>2.8</td>
      <td>16.0</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Frozen Corn</td>
      <td>0.18</td>
      <td>1/2 Cup</td>
      <td>72.2</td>
      <td>0.0</td>
      <td>0.6</td>
      <td>2.5</td>
      <td>17.1</td>
      <td>2.0</td>
      <td>2.5</td>
      <td>106.6</td>
      <td>5.2</td>
      <td>3.3</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Lettuce,Iceberg,Raw</td>
      <td>0.02</td>
      <td>1 Leaf</td>
      <td>2.6</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.8</td>
      <td>0.4</td>
      <td>0.3</td>
      <td>0.2</td>
      <td>66.0</td>
      <td>0.8</td>
      <td>3.8</td>
      <td>0.1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Neweng Clamchwd</td>
      <td>0.75</td>
      <td>1 C (8 Fl Oz)</td>
      <td>175.7</td>
      <td>10.0</td>
      <td>5.0</td>
      <td>1864.9</td>
      <td>21.8</td>
      <td>1.5</td>
      <td>10.9</td>
      <td>20.1</td>
      <td>4.8</td>
      <td>82.8</td>
      <td>2.8</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Tomato Soup</td>
      <td>0.39</td>
      <td>1 C (8 Fl Oz)</td>
      <td>170.7</td>
      <td>0.0</td>
      <td>3.8</td>
      <td>1744.4</td>
      <td>33.2</td>
      <td>1.0</td>
      <td>4.1</td>
      <td>1393.0</td>
      <td>133.0</td>
      <td>27.6</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>61</th>
      <td>New E Clamchwd,W/Mlk</td>
      <td>0.99</td>
      <td>1 C (8 Fl Oz)</td>
      <td>163.7</td>
      <td>22.3</td>
      <td>6.6</td>
      <td>992.0</td>
      <td>16.6</td>
      <td>1.5</td>
      <td>9.5</td>
      <td>163.7</td>
      <td>3.5</td>
      <td>186.0</td>
      <td>1.5</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Crm Mshrm Soup,W/Mlk</td>
      <td>0.65</td>
      <td>1 C (8 Fl Oz)</td>
      <td>203.4</td>
      <td>19.8</td>
      <td>13.6</td>
      <td>1076.3</td>
      <td>15.0</td>
      <td>0.5</td>
      <td>6.1</td>
      <td>153.8</td>
      <td>2.2</td>
      <td>178.6</td>
      <td>0.6</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Beanbacn Soup,W/Watr</td>
      <td>0.67</td>
      <td>1 C (8 Fl Oz)</td>
      <td>172.0</td>
      <td>2.5</td>
      <td>5.9</td>
      <td>951.3</td>
      <td>22.8</td>
      <td>8.6</td>
      <td>7.9</td>
      <td>888.0</td>
      <td>1.5</td>
      <td>81.0</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
<p>64 rows × 14 columns</p>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Calories</th>
      <th>Cholesterol mg</th>
      <th>Total_Fat g</th>
      <th>Sodium mg</th>
      <th>Carbohydrates g</th>
      <th>Dietary_Fiber g</th>
      <th>Protein g</th>
      <th>Vit_A IU</th>
      <th>Vit_C IU</th>
      <th>Calcium mg</th>
      <th>Iron mg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1500.0</td>
      <td>30.0</td>
      <td>20.0</td>
      <td>800.0</td>
      <td>130.0</td>
      <td>125.0</td>
      <td>60.0</td>
      <td>1000.0</td>
      <td>400.0</td>
      <td>700.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2500.0</td>
      <td>240.0</td>
      <td>70.0</td>
      <td>2000.0</td>
      <td>450.0</td>
      <td>250.0</td>
      <td>100.0</td>
      <td>10000.0</td>
      <td>5000.0</td>
      <td>1500.0</td>
      <td>40.0</td>
    </tr>
  </tbody>
</table>
</div>


Now, we will extract each column that corresponds to a specific nutrient:


```python
# Extract costs and nutrient values for each food from 'food_data` 

calories = food_data['Calories'].tolist()
cholesterol = food_data['Cholesterol mg'].tolist()
fat = food_data['Total_Fat g'].tolist()
sodium = food_data['Sodium mg'].tolist()
carbs = food_data['Carbohydrates g'].tolist()
fiber = food_data['Dietary_Fiber g'].tolist()
protein = food_data['Protein g'].tolist()
vit_a = food_data['Vit_A IU'].tolist()
vit_c = food_data['Vit_C IU'].tolist()
calcium = food_data['Calcium mg'].tolist()
iron = food_data['Iron mg'].tolist()
```

## Create the LP Object

The data has been organized, and the next step is to set up a linear programming (LP) problem in the Python library PuLP. PuLP is a convenient software that allows us to set up optimization models, define constraints, and choose solvers to find optimal solutions. 


```python
# PuLP Problem
prob = pulp.LpProblem("Diet_Optimization", sense=pulp.LpMinimize)

# Decision Variables
food_vars = [pulp.LpVariable(f"x_{food_data['Foods'][i]}", lowBound=0, cat='Continuous') for i in range(len(food_data))]
```

In this code, we have set up the LP Object and the decision variables for each food item has been designated by the optimization model. Each variable represents a decision we must make about how much of each food should we include in order to meet the daily nutrient constraints. 

## Setting Up the Objective Function


```python
# Objective function: Minimize total cost
food_costs = food_data['Price/ Serving'].tolist()

# Add or assign objective functino to the LP problem
prob += pulp.lpSum([food_costs [i] * food_vars[i] for i in range(len(food_vars))]), "Total Cost"
```

The objective function represents the quantity to be minimized or maximized in the optimization process. Each decision variable `food_vars[i]` is multiplied by the price per serving `food_costs[i]`, giving us the cost of that particular food item. We use a loop to make sure that every food item is included and added to the objective function. This will give us the final "Total Cost" of all the food items. 

## Adding Constraints (Min and Max Nutrients)


```python
# Add Calories Constraint
prob += pulp.lpSum([calories[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 0], "Min_Calories"
prob += pulp.lpSum([calories[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 0], "Max_Calories"

# Add Cholesterol mg Constraint
prob += pulp.lpSum([cholesterol[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 1], "Min_Cholesterol"
prob += pulp.lpSum([cholesterol[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 1], "Max_Cholesterol"

# Add Total_Fat g Constraint
prob += pulp.lpSum([fat[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 2], "Min_Fat"
prob += pulp.lpSum([fat[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 2], "Max_Fat"

# Add Sodium mg Constraint
prob += pulp.lpSum([sodium[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 3], "Min_Sodium"
prob += pulp.lpSum([sodium[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 3], "Max_Sodium"

# Add Carbohydrates g Constraint
prob += pulp.lpSum([carbs[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 4], "Min_Carbs"
prob += pulp.lpSum([carbs[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 4], "Max_Carbs"

# Add Dietary_Fiber g Constraint
prob += pulp.lpSum([fiber[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 5], "Min_Fiber"
prob += pulp.lpSum([fiber[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 5], "Max_Fiber"

# Add Protein g Constraint
prob += pulp.lpSum([protein[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 6], "Min_Protein"
prob += pulp.lpSum([protein[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 6], "Max_Protein"

# Add Vit_A IU Constraint
prob += pulp.lpSum([vit_a[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 7], "Min_VitA"
prob += pulp.lpSum([vit_a[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 7], "Max_VitA"

# Add Vit_C IU Constraint
prob += pulp.lpSum([vit_c[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 8], "Min_VitC"
prob += pulp.lpSum([vit_c[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 8], "Max_VitC"

# Add Calcium mg Constraint
prob += pulp.lpSum([calcium[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 9], "Min_Calcium"
prob += pulp.lpSum([calcium[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 9], "Max_Calcium"

# Add Iron mg Constraint
prob += pulp.lpSum([iron[i] * food_vars[i] for i in range(len(food_vars))]) >= nutrient_constraints.iloc[0, 10], "Min_Iron"
prob += pulp.lpSum([iron[i] * food_vars[i] for i in range(len(food_vars))]) <= nutrient_constraints.iloc[1, 10], "Max_Iron"

```

This is the code for the min/max nutrient constraints. Adding these constraints will make sure that our diet will meet the minimum and maximum nutrient requirements. For each nutrient, we once again rely on loops to get the nutrients from every food item in the data. 

## Solve and Retrieve the Outcome


```python
# Solve the problem 
prob.solve()

# Print the results
print("Status:", pulp. LpStatus [prob.status])
for v in food_vars:
    print(f"Optimal Quantity of {v.name}:", v. varValue) 
print("Minimum Cost:", pulp.value(prob.objective))
```

    Status: Optimal
    Optimal Quantity of x_Frozen_Broccoli: 0.25960653
    Optimal Quantity of x_Carrots,Raw: 0.0
    Optimal Quantity of x_Celery,_Raw: 52.64371
    Optimal Quantity of x_Frozen_Corn: 0.0
    Optimal Quantity of x_Lettuce,Iceberg,Raw: 63.988506
    Optimal Quantity of x_Peppers,_Sweet,_Raw: 0.0
    Optimal Quantity of x_Potatoes,_Baked: 0.0
    Optimal Quantity of x_Tofu: 0.0
    Optimal Quantity of x_Roasted_Chicken: 0.0
    Optimal Quantity of x_Spaghetti_W__Sauce: 0.0
    Optimal Quantity of x_Tomato,Red,Ripe,Raw: 0.0
    Optimal Quantity of x_Apple,Raw,W_Skin: 0.0
    Optimal Quantity of x_Banana: 0.0
    Optimal Quantity of x_Grapes: 0.0
    Optimal Quantity of x_Kiwifruit,Raw,Fresh: 0.0
    Optimal Quantity of x_Oranges: 2.2929389
    Optimal Quantity of x_Bagels: 0.0
    Optimal Quantity of x_Wheat_Bread: 0.0
    Optimal Quantity of x_White_Bread: 0.0
    Optimal Quantity of x_Oatmeal_Cookies: 0.0
    Optimal Quantity of x_Apple_Pie: 0.0
    Optimal Quantity of x_Chocolate_Chip_Cookies: 0.0
    Optimal Quantity of x_Butter,Regular: 0.0
    Optimal Quantity of x_Cheddar_Cheese: 0.0
    Optimal Quantity of x_3.3%_Fat,Whole_Milk: 0.0
    Optimal Quantity of x_2%_Lowfat_Milk: 0.0
    Optimal Quantity of x_Skim_Milk: 0.0
    Optimal Quantity of x_Poached_Eggs: 0.14184397
    Optimal Quantity of x_Scrambled_Eggs: 0.0
    Optimal Quantity of x_Bologna,Turkey: 0.0
    Optimal Quantity of x_Frankfurter,_Beef: 0.0
    Optimal Quantity of x_Ham,Sliced,Extralean: 0.0
    Optimal Quantity of x_Kielbasa,Prk: 0.0
    Optimal Quantity of x_Cap'N_Crunch: 0.0
    Optimal Quantity of x_Cheerios: 0.0
    Optimal Quantity of x_Corn_Flks,_Kellogg'S: 0.0
    Optimal Quantity of x_Raisin_Brn,_Kellg'S: 0.0
    Optimal Quantity of x_Rice_Krispies: 0.0
    Optimal Quantity of x_Special_K: 0.0
    Optimal Quantity of x_Oatmeal: 0.0
    Optimal Quantity of x_Malt_O_Meal,Choc: 0.0
    Optimal Quantity of x_Pizza_W_Pepperoni: 0.0
    Optimal Quantity of x_Taco: 0.0
    Optimal Quantity of x_Hamburger_W_Toppings: 0.0
    Optimal Quantity of x_Hotdog,_Plain: 0.0
    Optimal Quantity of x_Couscous: 0.0
    Optimal Quantity of x_White_Rice: 0.0
    Optimal Quantity of x_Macaroni,Ckd: 0.0
    Optimal Quantity of x_Peanut_Butter: 0.0
    Optimal Quantity of x_Pork: 0.0
    Optimal Quantity of x_Sardines_in_Oil: 0.0
    Optimal Quantity of x_White_Tuna_in_Water: 0.0
    Optimal Quantity of x_Popcorn,Air_Popped: 13.869322
    Optimal Quantity of x_Potato_Chips,Bbqflvr: 0.0
    Optimal Quantity of x_Pretzels: 0.0
    Optimal Quantity of x_Tortilla_Chip: 0.0
    Optimal Quantity of x_Chicknoodl_Soup: 0.0
    Optimal Quantity of x_Splt_Pea&Hamsoup: 0.0
    Optimal Quantity of x_Vegetbeef_Soup: 0.0
    Optimal Quantity of x_Neweng_Clamchwd: 0.0
    Optimal Quantity of x_Tomato_Soup: 0.0
    Optimal Quantity of x_New_E_Clamchwd,W_Mlk: 0.0
    Optimal Quantity of x_Crm_Mshrm_Soup,W_Mlk: 0.0
    Optimal Quantity of x_Beanbacn_Soup,W_Watr: 0.0
    Minimum Cost: 4.337116797399999
    

## Results

**Optimal Food Choices:**

Air-popped popcorn: 13.87 servings

Poached eggs: 0.14 servings

Oranges: 2.29 servings

Raw iceberg lettuce: 63.99 servings

Raw celery: 52.64 servings

Frozen broccoli: 0.26 servings


**Minimum Cost:** The cheapest combination of foods costs approximately $4.34


This is the cheapest combination of foods produced by the optimization model that meets all the nutritional requirements. The main bulk of the meal consists of popcorn, lettuce, and celery. Although this is in theory the cheapest possible meal, it is not a very realistic diet. We can't expect expect a soldier to eat over 60 servings of raw lettuce and 50 servings of raw celery with some popcorn. In the next question, we will add some additional constraints to modify this diet. 

## Modifying the Diet

In this part, we will add to our model the some new constraints:

a. If a food is selected, then a minimum of 1/10 serving must be chosen. 


```python
food_selected = [pulp.LpVariable(f"y_{i}", cat='Binary') for i in range(len(food_data))]

# Minimum serving size if a food is selected
for i in range(len(food_data)):
    prob += food_vars[i] >= 0.1 * food_selected[i], f"Min_Serving_{i}"
    
# Link binary and continuous variable so that if `y_i` = 0, `x_i` must also = 0
for i in range(len(food_data)):
    prob += food_vars[i] <= food_selected[i] * 100, f"Link_x_y_{i}" 
```

The code for this problem relies on binary variables to determine whether a food item is selected or not. The binary variable is then linked to a constraint making sure a food is selected, at least 1/10 will be included. 

b. Many people dislike celery and frozen broccoli. So at most one, but not both, can be selected.


```python
# Celery/Broccoli Constraint
prob += food_selected[2] + food_selected[0] <= 1, "Celery_Broccoli_Constraint"
```

Here, `food_selected[2]` is the binary variable for celery and `food_selected[0]` is the binary variable for broccoli. Making sure that the sum of these binary variables is less than equal ensures that at most one of them will be selected. 

c. To get day-to-day variety in protein, at least 3 kinds of meat/poultry/fish/eggs must be
selected.


```python
# Protein Constraints
proteins = [
    "Roasted Chicken",
    "Poached Eggs",
    "Scrambled Eggs",
    "Bologna, Turkey",
    "Frankfurter, Beef",
    "Ham, Sliced, Extralean",
    "Kielbasa, Pork",
    "Pork",
    "Sardines in Oil",
    "White Tuna in Water"
]

protein_indices = []

for i in range(len(food_data['Foods'])):
    if food_data['Foods'][i] in proteins:
        protein_indices.append(i)

print(protein_indices) 

prob += pulp.lpSum([food_selected[i] for i in protein_indices]) >= 3, "Protein_Variety_Constraint"
```

    [8, 27, 28, 30, 49, 50, 51]
    

For this problem, the following were selected as the main protein group: 

Roasted Chicken,
Poached Eggs,
Scrambled Eggs,
Bologna Turkey,
Frankfurter Beef
Ham Sliced Extralean,
Kielbasa Pork,
Pork,
Sardines in Oil,
White Tuna in Water

We first extract the indices of these protein food items and loop through them using the PuLP software. We designate that the sum of these protein binary variables will be equal to or greater than 3. This will ensure that at least 3 of these food items will be selected. 




## Solve the New Optimization Model


```python
prob.solve()

print("Status:", pulp.LpStatus[prob.status])
for v in food_vars:
    print(f"Optimal Quantity of {v.name}:", v.varValue)
print("Minimum Cost:", pulp.value(prob.objective))
```

    Status: Optimal
    Optimal Quantity of x_Frozen_Broccoli: 0.0
    Optimal Quantity of x_Carrots,Raw: 0.0
    Optimal Quantity of x_Celery,_Raw: 42.032842
    Optimal Quantity of x_Frozen_Corn: 0.0
    Optimal Quantity of x_Lettuce,Iceberg,Raw: 83.647733
    Optimal Quantity of x_Peppers,_Sweet,_Raw: 0.0
    Optimal Quantity of x_Potatoes,_Baked: 0.0
    Optimal Quantity of x_Tofu: 0.0
    Optimal Quantity of x_Roasted_Chicken: 0.0
    Optimal Quantity of x_Spaghetti_W__Sauce: 0.0
    Optimal Quantity of x_Tomato,Red,Ripe,Raw: 0.0
    Optimal Quantity of x_Apple,Raw,W_Skin: 0.0
    Optimal Quantity of x_Banana: 0.0
    Optimal Quantity of x_Grapes: 0.0
    Optimal Quantity of x_Kiwifruit,Raw,Fresh: 0.0
    Optimal Quantity of x_Oranges: 3.0746034
    Optimal Quantity of x_Bagels: 0.0
    Optimal Quantity of x_Wheat_Bread: 0.0
    Optimal Quantity of x_White_Bread: 0.0
    Optimal Quantity of x_Oatmeal_Cookies: 0.0
    Optimal Quantity of x_Apple_Pie: 0.0
    Optimal Quantity of x_Chocolate_Chip_Cookies: 0.0
    Optimal Quantity of x_Butter,Regular: 0.0
    Optimal Quantity of x_Cheddar_Cheese: 0.0
    Optimal Quantity of x_3.3%_Fat,Whole_Milk: 0.0
    Optimal Quantity of x_2%_Lowfat_Milk: 0.0
    Optimal Quantity of x_Skim_Milk: 0.0
    Optimal Quantity of x_Poached_Eggs: 0.1
    Optimal Quantity of x_Scrambled_Eggs: 0.1
    Optimal Quantity of x_Bologna,Turkey: 0.0
    Optimal Quantity of x_Frankfurter,_Beef: 0.1
    Optimal Quantity of x_Ham,Sliced,Extralean: 0.0
    Optimal Quantity of x_Kielbasa,Prk: 0.0
    Optimal Quantity of x_Cap'N_Crunch: 0.0
    Optimal Quantity of x_Cheerios: 0.0
    Optimal Quantity of x_Corn_Flks,_Kellogg'S: 0.0
    Optimal Quantity of x_Raisin_Brn,_Kellg'S: 0.0
    Optimal Quantity of x_Rice_Krispies: 0.0
    Optimal Quantity of x_Special_K: 0.0
    Optimal Quantity of x_Oatmeal: 0.0
    Optimal Quantity of x_Malt_O_Meal,Choc: 0.0
    Optimal Quantity of x_Pizza_W_Pepperoni: 0.0
    Optimal Quantity of x_Taco: 0.0
    Optimal Quantity of x_Hamburger_W_Toppings: 0.0
    Optimal Quantity of x_Hotdog,_Plain: 0.0
    Optimal Quantity of x_Couscous: 0.0
    Optimal Quantity of x_White_Rice: 0.0
    Optimal Quantity of x_Macaroni,Ckd: 0.0
    Optimal Quantity of x_Peanut_Butter: 1.8980618
    Optimal Quantity of x_Pork: 0.0
    Optimal Quantity of x_Sardines_in_Oil: 0.0
    Optimal Quantity of x_White_Tuna_in_Water: 0.0
    Optimal Quantity of x_Popcorn,Air_Popped: 13.247788
    Optimal Quantity of x_Potato_Chips,Bbqflvr: 0.0
    Optimal Quantity of x_Pretzels: 0.0
    Optimal Quantity of x_Tortilla_Chip: 0.0
    Optimal Quantity of x_Chicknoodl_Soup: 0.0
    Optimal Quantity of x_Splt_Pea&Hamsoup: 0.0
    Optimal Quantity of x_Vegetbeef_Soup: 0.0
    Optimal Quantity of x_Neweng_Clamchwd: 0.0
    Optimal Quantity of x_Tomato_Soup: 0.0
    Optimal Quantity of x_New_E_Clamchwd,W_Mlk: 0.0
    Optimal Quantity of x_Crm_Mshrm_Soup,W_Mlk: 0.0
    Optimal Quantity of x_Beanbacn_Soup,W_Watr: 0.0
    Minimum Cost: 4.524234696
    

**Optimal Food Choices**

Raw Celery: 42.03 grams

Raw Iceburg Lettuce: 83.65 grams

Oranges: 3.07 grams

Poached Eggs: 0.1 servings

Scrambled Eggs: 0.1 servings

Frankfurter Beef: 0.1 servings

Peanut Butter: 1.90 grams

Air Popped Popcorn: 13.25 grams


**Minimum Cost**: The cheapest combination of foods costs approximately $4.52

## Conclusion

This is the new optimal meal that includes all of the new constraints. It is a slightly more feasible meal in terms of diversity. Peanut butter, poached eggs, scrambled eggs, and frakfurter beef was added to the meal. The model has chosen to keep the lettuce and take out broccoli. It seems that this new diet plan is a bit more expensive than that of our previous model, but not by a significant amount (about 18 cents). The meal does meet the nutritional requirements but it still doesn't look like a realistic diet. The meal relies heavily on raw celery and raw iceburg lettuce to meet the constraints, and it feels like everything else is there for garnish. If desired, we can further this analysis by increasing the amount of other foods to make it a more balanced meal. We can do this by requiring additional constraints added to the model. 

Mathematics and computer software may give us a perfect meal in theory, but it is not a meal that we could use in a realistic diet plan. The model may provide a mathematically ideal answer, but models must often be adjusted for practicality and context. This is an important lesson in optimization modeling. In order to fully utilize optimization models to real life situations, human judgment and wisdom is required. Like any aspect of technology, optimization is a tool and the user is the one who decides how to best apply the results. 
