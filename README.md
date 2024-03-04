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

#### **Problem Description and public test**
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/a340a93e-ad90-44b6-8ddc-960c4855a2e6)
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/9157bf8c-1b89-4fa6-bee4-6d5a12ab5b7a)
![image](https://github.com/TruongQuynhNhu/ApplyAlphaCodiumforCodeGeneration/assets/107611691/641b99b4-27de-4230-9e94-afed2b8ef274)

The problem description and public tests are input as json file.

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

