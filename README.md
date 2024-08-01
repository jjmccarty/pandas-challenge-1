# pandas-challenge-1

This pandas challenge asks the developer to load a csv file of data into a DataFrame and utilizes that dataframe to explore the data, prepare it for analysis and modify the data for display in 4 parts.  

> Note that this work is provided in the form of a Jupyter Notebook which assumes that all steps are run in succession for successful completion of the program. 

## Step 1: Explore the Data

Initial work in exploration of the data includes getting general information about the data that was imported including

1. Using DataFrame.columns to retrieve the columns names in the DataFrame
2. Using DataFrame.describe() method to get statistics on the DataFrame data
3. Using DataFrame.info() method to get information on the columns and datatypes of the columns

### Evaluating grouped data
The next portion of the exercise is based on getting data about the categories and subcategories that have the most entry for the data.  

While this could be done by sorting data and iterating through values to determine the highest amounts for the categories, review of the API allowed for other options. 
 
In this case we utilized the DataFrame.GroupBy function to automatically group the data by a column and then utilizing the size functions to identify the 3 most utilized categories. 

For Example
```
topCats = df.groupby('category').size().nlargest(n=3, keep='all')
```

Similar logic was used to further refine the data based on the most common subcategories

```
topSubcat = h_cat_df.groupby('subcategory').size().nlargest(n=1, keep='all')
```

### Identifying top clients
The final portion of the step 1 work asks to identify the top clients based on amount of line items. 

We utilize the same GroupBy functions to identify the top 5 clients.  Because this already ordered by qty purchased we can simply pull the first 5 clients from the list. 

```
top_client = df.groupby('client_id').size().nlargest(n=5, keep='all')
```

> Note care must be taken when utilizing GroupBy with the nlargest() function and the keep=all parameter.  Even though we specify that we want the first 5 clients, if a client had the same amount of qty this could return more than 5 items, however in our dataset this is not a condition. 

Now that we know the top clients this can be processed into a list and displayed. 

We are asked to calculate the total units purchased from the top client.  As this is the first item from the GroupBy logic we can simply grab that id and use it to ```sum``` the line items retrieved using the ```DataFrame.loc``` with the given id. 

## Step 2: Transform the Data
Step 2 prepares the data for analysis by adding new columns calculated from existing data. For example, the following will be calculated and added:

1. ```line_subtotal``` from ```unit_price``` ** ```qty```
2. ```total_weight``` from ```unity_price``` * ```qty```
3. ```shipping_weight``` from ```total_weight``` * $7 if weight is greater than 50 or $10 for lesser weight 
4. ```line_price```  from ```line_subtotal``` + ```shipping_price``` + tax
5. ```line_cost``` from ```unit_cost``` * ```qty``` + ```shipping_price```
6. ```line_profit``` from ```line_price``` = ```line-cost```

Columns are simply calculated and added to the original DataFrame


## Step 3: Confirming Data
To confirm our current work so far we are asked to verify the line item information for the top clients.  

This is simply done by using ```DataFrame.loc``` with the given clients ids to retrieve our calculated ```line_price``` against the expected result.

```
order2742071 = round(sum(df.loc[ df['order_id'] == 2742071 ]['line_price']),2)
print(f"Order 2742071 total price: {order2742071}")
```

## Step 4: Analyze the data
Step 4 uses the new columns to analyze and format the client data for the user. 

Initially we identify the amount of spend for each of the top 5 clients.  The top 5 clients were initially identified in prior steps and we store the spend in a dictionary keyed by the client id. 

Next we create a DataFrame specific to the top 5 clients with the sum of the line items for each purchase.  Including the following columns 

1. client_id	
2. qty	
3. shipping_price	
4. line_price	
5. line_cost	
6. line_profit

Data is then converted to millions and the column title are reformated. 

```
format_top_client_df = top_client_df.rename(columns={
    "client_id":"Client ID",
    "qty":"Units",
    "shipping_price":"Shipping (millions)",
    "line_price":"Total Revenue (millions)",
    "line_cost":"Total Cost (millions)",
    "line_profit":"Total Profit (millions)"
})
```
 
 Data is then sorted by the total revenue

 ```
 sorted_top_client = format_top_client_df.sort_values(by='Total Revenue (millions)', ascending=False)
 ```







