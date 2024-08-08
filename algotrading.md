
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

#Trading algorithm
for (i in 1:nrow(amd_df)) { current_price <- amd_df$close[i]
if (previous_price == 0) {
amd_df$trade_type[i] <- "buy"
amd_df$costs_proceeds[i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
} else
if (current_price < previous_price) { amd_df$trade_type[i] <- "buy"
amd_df$costs_proceeds [i] <- -current_price * share_size
accumulated_shares <- accumulated_shares + share_size }
if (i == nrow(amd_df)) {
amd_df$trade_type[i] <- "sell"
amd_df$costs_proceeds[i] <- current_price * accumulated_shares accumulated_shares <- 0
}
previous_price <- current_price amd_df$accumulated_shares[i] <- accumulated_shares
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Defining a trading period in the past five years
subset_df <- amd_df[489:681,1:5] # start period: 2021-04-27, end period: 2022-01-28
# Initial conditions for set period
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
#Trading Algorithm for set period
for (i in 1:nrow(subset_df)) { current_price <- subset_df$close[i]
if (previous_price == 0) {
subset_df$trade_type[i] <- "buy" subset_df$costs_proceeds[i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
} else if (current_price < previous_price) { subset_df$trade_type[i] <- "buy"
subset_df$costs_proceeds [i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
}
if (i == nrow(subset_df)) {
    #final trade period
subset_df$trade_type[i] <- "sell"
subset_df$costs_proceeds[i] <- current_price * accumulated_shares accumulated_shares <- 0
}
previous_price <- current_price subset_df$accumulated_shares[i] <- accumulated_shares
}
#Viewing Data
View(subset_df)
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Total profit/loss calculation
profit_loss <- sum(subset_df$costs_proceeds,na.rm = TRUE)
# Investment Captial
Trades <- subset_df$costs_proceeds[subset_df$trade_type == "buy"] Total_invested_captial <- -sum(Trades,na.rm = TRUE)
#ROI Calculation
ROI <- (profit_loss / Total_invested_captial) * 100
#Results
print(paste("Total Profit/Loss:", profit_loss))
print(paste("Total Invested Capital:", Total_invested_captial))
print(paste("ROI:", ROI,"%"))
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# Defining profit-taking threshold percentage
profit_taking_threshold <- 0.25
# Initial conditions for period
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
# Trading algorithm
for (i in 1:nrow(subset_df)) { current_price <- subset_df$close [i]
if (previous_price == 0) {
# Initial condition
subset_df$trade_type[i] <- "buy"
subset_df$costs_proceeds [i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size average_purchase_price <- current_price
}
else if (current_price < previous_price) {
   # Continued purchase of shares
subset_df$trade_type[i] <- "buy"
subset_df$costs_proceeds [i] <- -current_price * (share_size) accumulated_shares <- accumulated_shares + share_size
}
else if (current_price >= (1+profit_taking_threshold)*average_purchase_price){
   # Profit-taking conditions
subset_df$trade_type[i] <- "sell"
subset_df$costs_proceeds [i] <- (current_price * accumulated_shares)/2 # Selling half of the shares in holding
accumulated_shares <- accumulated_shares/2 }
else {
# Remaining shares are in holding as they doesn't meet the above
conditions
subset_df$trade_type [i] <- "holdings"
costs_proceeds <- subset_df$close[i] }
previous_price <- current_price subset_df$accumulated_shares [i] <- accumulated_shares}
 #Final trade period
if (i == nrow(subset_df)) {
# All remaining shares are sold
subset_df$trade_type[i] <- "sell"
subset_df$costs_proceeds[i] <- current_price * accumulated_shares accumulated_shares <- 0}
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# profit_loss <- sum(subset_df$costs_proceeds,na.rm = TRUE)
# Investment Captial of chosen period
Trades <- subset_df$costs_proceeds[subset_df$trade_type == "buy"] Total_invested_captial <- -sum(Trades,na.rm = TRUE)
#ROI Calculation of chosen period
ROI <- (profit_loss / Total_invested_captial) * 100 #Results
print(paste("Total Profit/Loss:", profit_loss))
print(paste("Total Invested Capital:", Total_invested_captial))
print(paste("ROI:", ROI,"%"))
```

Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su discussed a new graphics processor designed for AI servers, with Microsoft and Meta as committed users. The rise in AMD shares on the following Thursday suggests that investors believe in the chipmaker's upward potential and market expectations; My first strategy earned X dollars more than second strategy on this day, therefore providing a better ROI.


During the height of Covid-19 second wave during late 2021 and early 2022, it caused massive disruptions with the enforced lock down measures causing increased remote working and unemployment. With the mass skepticism of the economic downturn as well as the cyclical decline in PC sales in late 2021, this caused AMD’s earnings to fall. Hence, investors were disappointed by AMD’s performance which resulted in AMD’s share prices falling. From modifying the trading excel strategy to follow the profit-taking strategy, it significantly improved my P/L and ROI values. This is seen in my first strategy earning a loss of $51,378.01, while the second strategy earned a profit of $89,838.24. Hence, the second strategy resulted in a better ROI of 8.38%, as the first strategy generated a return of -4.79%. As AMD’s stocks were not consistently falling, through the profit-taking strategy of selling stocks when a profit is generated, and holding until the prices are low again to buy, it reduced the amount of loss incurred, thus allowing for a positive ROI.

