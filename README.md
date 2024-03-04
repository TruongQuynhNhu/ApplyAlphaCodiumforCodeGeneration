# ApplyAlphaCodiumforCodeGeneration
Apply AlphaCodium (of AICodium) for Trading Strategy and Backtest problem

Reference: [Paper](https://arxiv.org/abs/2401.08500) |
[Dataset](https://huggingface.co/datasets/talrid/CodeContests_valid_and_test_AlphaCodium/blob/main/codecontests_valid_and_test_processed_alpha_codium.zip)


## 1. Installation

(0) clone AlphaCodium Github:git clone https://github.com/Codium-ai/AlphaCodium.git

(1) setup a virtual environment and run: pip install -r requirements.txt

(2) Duplicate the file alpha_codium/settings/.secrets_template.toml, rename it as .secrets.toml, and fill in your OpenAI API key:

[openai]
key = "..."

## 2. How to run

### 2.1. Configuration
The file: `alpha_codium/settings/configuration.toml` contains the configuration for the project.
In the `config` section you can choose the model you want to use ("gpt-4", "gpt-3.5-turbo-16k", or others).

### 2.2. Solving a new problem (CodeContest format)
To solve a custom problem with AlphaCodium, first create a json file that includes the CodeContest problem fields, and then from the root folder run:
```
python -m alpha_codium.solve_my_problem \
--my_problem_json_file /path/to/my_problem.json
```
- The `my_problem_json_file` is the path to to the custom problem json file.

See the `my_problem_example.json` to see an example of a custom problem. The json file should include the following fields:
- `name` is the name of the problem.
- `description` is a description of the problem.
- (optional) `public_tests` with the following fields:
  - `input` is a list of strings that represent the input.
  - `output` is a list of strings that represent the output.
- (optional) `private_tests`, that follows the same structure as `public_tests`
- (optional) `generated_tests`, that follows the same structure as `public_tests`

### 2.3. Apply to the problem of executing trading strategy and backtesting

This project applies the available AlphaCodium flow to generate code corresponding to a specific trading strategy using `SMA5` and `SMA22` indicators as follows:

- When the 5-day moving average crosses above the 22-day moving average and the closing price is higher than the 5-day moving average: buy signal.
- When the 5-day moving average crosses below the 22-day moving average: sell signal.
Then, create code to backtest the chosen strategy.

#### **Problem Description and public test**: is developed as Json file
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/ab839ace-3a29-4f08-a8e7-eee08cd0d8fe)
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/9157bf8c-1b89-4fa6-bee4-6d5a12ab5b7a)
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/641b99b4-27de-4230-9e94-afed2b8ef274)

{
  "name": "Investment Strategy Backtest",
  "description": "You are tasked to build a backtest function, simulating the execution of a trading strategy using historical stock data.\nFunction Definition: the function take three parameters: data, initial_capital, and shares_per_signal. The default value of initial_capital is 100000000 and the default value of share_per_signal is 100.\nData is a dataframe consisting of an index column named ‘Datetime’ (not a numerical index) and 5 columns, including: Close: the closing price for each day, SMA5, SMA22, Buysignal, Sellsignal.\nThe function will return a tracking table derived from the original data (index column named Datetime plus 5 columns Close, SMA5, SMA22, Buysignal, Sellsignal), with additional columns as the following (Position_Buy, Position_Sell, Trade_Action, Capital,Shares_Held, Market_Return, Cumulative_Market_Returns,Strategy_Value,Cumulative_Strategy_Returns)\nInitialization:\nInitialize two variable: \nCapital: set it to the value of initial_Zapital, this is the initial amount of capital available for trading.\nShares_Held: initialize it to 0, representing the number of shares currently held in the porfolio.\nDetermine Buying and Selling Position:\nPosition_Buy: Shift the value of the Buysignal column\nPosition_Sell: Shift the value of the Sellsignal column\nThen iterates through each row of the DataFrame to fill in the remaining columns according to the chronological order of time, starting from the earliest day and proceeding to the following days..\nTrade_Action: Indicates whether a buy or sell action occurred on that day. Initialize it as an empty string, it means that, there is no action done on that day. The trading action is “buy” if all conditions are met: Position_Buy is True, and there is sufficient capital (the capital of the previous day >= today’s closing price * shares_per_signal). Conversely, the trading action is “sell” if all conditions are met: Position_Sell is True, and there are shares currently held. If there are action executed, remember to update the capital and shares held accordingly.\nCapital: This variable represents the available capital. If no trading action occurs, the value remains unchanged. However, if a trading action occurs, it is adjusted by subtracting or adding the price of the shares bought or sold from the previous day's capital.\nShares_Held: This represents the number of shares currently held. If no trading action occurs, this value remains unchanged. However, if a trading action occurs, it is adjusted by adding or subtracting the 'shares_per_signal' value from the number of shares held on the previous day.\nMarket_Return: The percentage change in the closing price compared to the previous day.\nCumulative_Market_Returns: The percentage change in the closing price from the first to the last day.\nStrategy_Value: The total current value of the portfolio, calculated as the sum of available cash capital and the total value of held shares based on the current stock price.\nCumulative_Strategy_Returns: The current profit of the portfolio according to the strategy, expressed as the fraction of change in the total current value compared to the initial capital.\n\nAfter computation, round the values in the following columns to the 6th decimal place: 'Market_Return', 'Cumulative_Market_Returns', and 'Cumulative_Strategy_Returns' columns . Finally, return final table.\n\nThe input and output formats for the public tests are as follows:\n\nInput:\nThe first line indicates the name of the index (Datetime) and the name of the column (Close, SMA5, SMA22, BuySignal, SellSignal).\n\nEach subsequent line (from the second line onward) represents a date, its closing price, SMA5, SMA22, Buy signal and Sell signal.\n\nOutput:\n\nThe first line specifies the name of the index (Datetime) and the names of the columns (Close, SMA5, SMA22, BuySignal, SellSignal, Position_Buy, Position_Sell, Trade_Action, Capital, Shares_Held, Market_Return, Cumulative_Market_Returns, Strategy_Value, Cumulative_Strategy_Returns). Each subsequent line corresponds to a day.\n\nHere's an illustration using the first example:\n\nInput\nDatetime|Close|SMA5|SMA22|BuySignal|SellSignal\n2023-09-05|90100.0|NaN|NaN|False|False\n2023-09-06|90200.0|NaN|NaN|False|False\n2023-09-07|89500.0|NaN|NaN|False|False\n2023-09-08|89500.0|NaN|NaN|False|False\n\nOutput\n\nDatetime|Close|SMA5|SMA22|BuySignal|SellSignal|Position_Buy|Position_Sell|Trade_Action|Capital|Shares_Held|Market_Return|Cumulative_Market_Returns|Strategy_Value|Cumulative_Strategy_Returns\n2023-09-05|90100.0|NaN|NaN|False|False|NaN|NaN||100000000|0|NaN|NaN|100000000.0|1.0\n2023-09-06|90200.0|NaN|NaN|False|False|False|False||100000000|0|0.001110|1.001110|100000000.0|1.0\n2023-09-07|89500.0|NaN|NaN|False|False|False|False||100000000|0|-0.007761|0.993341|100000000.0|1.0\n2023-09-08|89500.0|NaN|NaN|False|False|False|False||100000000|0|0.000000|0.993341|100000000.0|1.0\n",
  "public_tests": {
    "input": [
      "Datetime|Close|SMA5|SMA22|BuySignal|SellSignal\n2022-01-04|66892.460938|NaN|NaN|False|False\n2022-01-05|66045.726562|NaN|NaN|False|False\n2022-01-06|66977.140625|NaN|NaN|False|False\n2022-01-07|67569.859375|NaN|NaN|False|False\n2022-01-10|67315.835938|66960.204687|NaN|False|False\n2022-01-11|67231.156250|67027.943750|NaN|False|False\n2022-01-12|67739.203125|67366.639063|NaN|False|False\n2022-01-13|69348.007812|67840.812500|NaN|False|False\n2022-01-14|70618.117188|68450.464063|NaN|False|False\n2022-01-17|72988.992188|69585.095312|NaN|False|False\n2022-01-18|74174.429688|70973.750000|NaN|False|False\n2022-01-19|73497.039062|72125.317187|NaN|False|False\n2022-01-20|73751.054688|73005.926562|NaN|False|False\n2022-01-21|75529.210938|73988.145312|NaN|False|False\n2022-01-24|78746.828125|75139.712500|NaN|False|False\n2022-01-25|81117.695312|76528.365625|NaN|False|False\n2022-01-26|80016.937500|77832.345312|NaN|False|False\n2022-01-27|77053.343750|78492.803125|NaN|False|False\n2022-01-28|75359.867188|78458.934375|NaN|False|False\n2022-02-07|78238.781250|78357.325000|NaN|False|False\n2022-02-08|77900.085938|77713.803125|NaN|False|False\n2022-02-09|76884.000000|77087.215625|72954.353338|False|False\n2022-02-10|77307.367188|77138.020312|73427.758168|True|False\n2022-02-11|76206.601562|77307.367188|73889.616122|False|False\n2022-02-14|72819.640625|76223.539062|74155.184304|False|False"
    ],
    "is_valid_test": null,
    "output": [
      "Datetime|Close|SMA5|SMA22|BuySignal|SellSignal|Position_Buy|Position_Sell|Trade_Action|Capital|Shares_Held|Market_Return|Cumulative_Market_Returns|Strategy_Value|Cumulative_Strategy_Returns\n2022-01-04|66892.460938|NaN|NaN|False|False|NaN|NaN||1.000000e+08|0|NaN|NaN|1.000000e+08|1.000000\n2022-01-05|66045.726562|NaN|NaN|False|False|False|False||1.000000e+08|0|-0.012658|0.987342|1.000000e+08|1.000000\n2022-01-06|66977.140625|NaN|NaN|False|False|False|False||1.000000e+08|0|0.014103|1.001266|1.000000e+08|1.000000\n2022-01-07|67569.859375|NaN|NaN|False|False|False|False||1.000000e+08|0|0.008850|1.010127|1.000000e+08|1.000000\n2022-01-10|67315.835938|66960.204687|NaN|False|False|False|False||1.000000e+08|0|-0.003759|1.006329|1.000000e+08|1.000000\n2022-01-11|67231.156250|67027.943750|NaN|False|False|False|False||1.000000e+08|0|-0.001258|1.005063|1.000000e+08|1.000000\n2022-01-12|67739.203125|67366.639063|NaN|False|False|False|False||1.000000e+08|0|0.007557|1.012658|1.000000e+08|1.000000\n2022-01-13|69348.007812|67840.812500|NaN|False|False|False|False||1.000000e+08|0|0.023750|1.036709|1.000000e+08|1.000000\n2022-01-14|70618.117188|68450.464063|NaN|False|False|False|False||1.000000e+08|0|0.018315|1.055696|1.000000e+08|1.000000\n2022-01-17|72988.992188|69585.095312|NaN|False|False|False|False||1.000000e+08|0|0.033573|1.091139|1.000000e+08|1.000000\n2022-01-18|74174.429688|70973.750000|NaN|False|False|False|False||1.000000e+08|0|0.016241|1.108861|1.000000e+08|1.000000\n2022-01-19|73497.039062|72125.317187|NaN|False|False|False|False||1.000000e+08|0|-0.009132|1.098734|1.000000e+08|1.000000\n2022-01-20|73751.054688|73005.926562|NaN|False|False|False|False||1.000000e+08|0|0.003456|1.102532|1.000000e+08|1.000000\n2022-01-21|75529.210938|73988.145312|NaN|False|False|False|False||1.000000e+08|0|0.024110|1.129114|1.000000e+08|1.000000\n2022-01-24|78746.828125|75139.712500|NaN|False|False|False|False||1.000000e+08|0|0.042601|1.177215|1.000000e+08|1.000000\n2022-01-25|81117.695312|76528.365625|NaN|False|False|False|False||1.000000e+08|0|0.030107|1.212658|1.000000e+08|1.000000\n2022-01-26|80016.937500|77832.345312|NaN|False|False|False|False||1.000000e+08|0|-0.013570|1.196203|1.000000e+08|1.000000\n2022-01-27|77053.343750|78492.803125|NaN|False|False|False|False||1.000000e+08|0|-0.037037|1.151899|1.000000e+08|1.000000\n2022-01-28|75359.867188|78458.934375|NaN|False|False|False|False||1.000000e+08|0|-0.021978|1.126582|1.000000e+08|1.000000\n2022-02-07|78238.781250|78357.325000|NaN|False|False|False|False||1.000000e+08|0|0.038202|1.169620|1.000000e+08|1.000000\n2022-02-08|77900.085938|77713.803125|NaN|False|False|False|False||1.000000e+08|0|-0.004329|1.164557|1.000000e+08|1.000000\n2022-02-09|76884.000000|77087.215625|72954.353338|False|False|False|False||1.000000e+08|0|-0.013043|1.149367|1.000000e+08|1.000000\n2022-02-10|77307.367188|77138.020312|73427.758168|True|False|False|False||1.000000e+08|0|0.005507|1.155696|1.000000e+08|1.000000\n2022-02-11|76206.601562|77307.367188|73889.616122|False|False|True|False|Buy|9.237934e+07|100|-0.014239|1.139241|1.000000e+08|1.000000\n2022-02-14|72819.640625|76223.539062|74155.184304|False|False|False|False||9.237934e+07|100|-0.044444|1.088608|9.966130e+07|0.996613\n"
    ]
  },
  "private_tests": {
    "input": [],
    "is_valid_test": null,
    "output": []
  },
  "generated_tests": {
  }
}

### 2.4.Reflection

After completing this project, several conclusions have been drawn:

- It's crucial to take note of the original values when running the code, compare data, calculate results, and display rounded values to an appropriate number of decimal places. This ensures accurate comparisons.

- Creating AI tests can be prone to errors, particularly when dealing with numerical problems like the ones encountered in this project.

- The problem description is of utmost importance; it should be precise, comprehensive, and not overly lengthy. Additionally, special attention should be given to characters such as escape characters and punctuation marks (-, ", ', `), as they can disrupt the flow of prompts and hinder productivity in responding to prompts. Since the AI operates through multiple intermediate steps, an unclear or vague description can amplify errors and prevent convergence toward a correct solution.

- Despite numerous attempts, certain bugs persist. In particular, with the backtesting issue, I manually inspected ChatGPT's code and adjusted the problem description to account for edge cases.

- Proper machine configuration is essential for rerunning existing code package effectively.


## 3. Citation
```
@misc{ridnik2024code,
      title={Code Generation with AlphaCodium: From Prompt Engineering to Flow Engineering}, 
      author={Tal Ridnik and Dedy Kredo and Itamar Friedman},
      year={2024},
      eprint={2401.08500},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}
```

