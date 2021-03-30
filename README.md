# Training Data-Driven or Surrogate Simulators

This repository provides a template for training data-driven simulators that can then be leveraged for training brains (reinforcement learning agents) with [Project Bonsai](https://docs.bons.ai/).

:warning: Disclaimer: This is not an official Microsoft product. This application is considered an experimental addition to Microsoft's Project Bonsai toolbox. Its primary goal is to reduce barriers of entry to use Project Bonsai's core Machine Teaching. Pull requests for fixes and small enhancements are welcome, but we expect this to be replaced by out-of-the-box features of Project Bonsai shortly.

## Dependencies

This repository leverages [Anaconda](https://docs.conda.io/en/latest/miniconda.html) for Python virtual environments and all dependencies. Please install Anaconda or miniconda first and then run the following:

```bash
conda env update -f environment.yml
conda activate ddm
```

This will create and activate a new conda virtual environment named `ddm` based on the configuration in the [`environment.yml`](environment.yml) file.

## Tests

To get an understanding of the package, you may want to look at the tests in [`tests`](./tests), and the configuration files in [`conf`](./conf). You can run the tests by simply:

```bash
pytest tests
# or
python -m pytest tests/
```

## Usage

The scripts in this package expect that you have a dataset of CSVs or numpy arrays. If you are using a CSV, you should ensure that:

- The CSV has a header with unique column names describing your inputs to the model and the outputs of the model.
- The CSV should have a column for the episode index and another column for the iteration index.
- The CSV should have been cleaned from any rows containing NaNs

### Generating Logs from an Existing Simulator

For an example on how to generate logged datasets from a simulator using the Python SDK, take a look at the examples in the [samples repository](https://github.com/microsoft/microsoft-bonsai-api/tree/main/Python/samples), in particular, you can use the flag `--test-local True --log-iteration True` to generate a CSV data that matches the schema used in this repository.

### Training Your Models

The scripts in this package leverage the configuration files saved in the [`conf`](./conf) folder to load CSV files, train and save models, and interface them to the Bonsai service. There are three configuration files:
- conf/data/$YOUR_DATA_CONFIG.yaml defines the interface to the data to train on
- conf/model/$YOUR_MODEL_CONFIG.yaml defines the Machine Learning model's hyper-parameters
- conf/simulator/$YOUR_SIM_CONFIG.yaml defines the simulator interface

The library comes with a default configuration set in [`conf/config.yaml`](conf/config.yaml).

```bash
python ddm_trainer.py
```

You can change any configuration parameter by specifying the configuration file you would like to change and its new path, i.e.,

```bash
python ddm_trainer.py data=cartpole_st_at simulator=gboost_cartpole.yaml
```

which will use the configuration files in [`conf/data/cartpole_st_at.yaml`](./conf/data/cartpole_st_at.yaml) and [`conf/simulator/gboost_cartpole.yaml`](./conf/simulator/gboost_cartpole.yaml).

You can also override the parameters of the configuration file by specifying their name:

```bash
python ddm_trainer.py data.path=csv_data/cartpole_at_st.csv data.iteration_order=1
python ddm_trainer.py data.path=csv_data/cartpole_at_st.csv model=xgboost 
```

The script automatically saves your model to the path specified by `model.saver.filename`. An `outputs` directory is also saved with your configuration file and logs.

### Building Your Simulators

The schema for your simulator resides in [`conf/simulator`](./conf/simulator). After defining your states, actions, and configs, you can run the simulator as follows:

```bash
python ddm_predictor simulator=$YOUR_SIM_CONFIG.yaml
```

If you would like to test your simulator before connecting to the platform, you can use a random policy:

```bash
python ddm_predictor.py simulator=$YOUR_SIM_CONFIG.yaml simulator.policy=random
```

## Build Simulator Package

```bash
az acr build --image <IMAGE_NAME>:<IMAGE_VERSION> --file Dockerfile --registry <ACR_REGISTRY> .
```

## Data Evaluation

Use the release version of the jupyter notebook to assist you with qualifying your data for creation of a simulator using supervised learning. The notebook is split up into three parts. The notebook uses the `nbgrader` package, where the user should click the `Validate` button to determine if all tests have been passed. You will be responsible for loading the data, running cells to see if you successfully pass tests, and manipulating the data in Python if the notebook finds things like NaNs and outliers. It will ask for desired operating limits of the model you wish to create and compare that against what is available in your provided datasets. Asssuming you pass the tests for data relevance, your data will be exported to a single csv named `approved_data.csv` which is ready to be ingested by the `datadrivenmodel` tool.

- Data Relevance
- Sparsity
- Data Distribution Confidence

```bash
jupyter notebook release/presales_evaluation/presales_evaluation.ipynb
```
![](img/presales_notebook.PNG)

> Once you have successfuly qualified your data using the `Validate` button, it is recommended to export it as a PDF to share the results without requiring access to the data.

## Contribute Code
This project welcomes contributions and suggestions. Most contributions require you to
agree to a Contributor License Agreement (CLA) declaring that you have the right to,
and actually do, grant us the rights to use your contribution. For details, visit
https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need
to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the
instructions provided by the bot. You will only need to do this once across all repositories using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)
or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Telemetry
The software may collect information about you and your use of the software and send it to Microsoft. Microsoft may use this information to provide services and improve our products and services. You may turn off the telemetry as described in the repository. There are also some features in the software that may enable you and Microsoft to collect data from users of your applications. If you use these features, you must comply with applicable law, including providing appropriate notices to users of your applications together with a copy of Microsoft's privacy statement. Our privacy statement is located at https://go.microsoft.com/fwlink/?LinkID=824704. You can learn more about data collection and use in the help documentation and our privacy statement. Your use of the software operates as your consent to these practices.

## Trademarks
This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft trademarks or logos is subject to and must follow Microsoft's Trademark & Brand Guidelines. Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship. Any use of third-party trademarks or logos are subject to those third-party's policies.