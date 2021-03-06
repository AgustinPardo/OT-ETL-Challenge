# Open Targets Software Developer Technical Test

## Prerequistes
* Python 3.9.6
* git
* wget


## Follow the next steps to get ready to run the application:
1 [Get source code](#1)

2 [Download datasets](#2)

3 [Setting up environment](#3)

4 [Running application](#4)

5 [Testing](#5)

<a name="1"></a>
## 1) Get source code

Clone source code. Execute:
```bash
git clone https://github.com/AgustinPardo/OT-ETL-Challenge
```

Go to repository folder. Execute:
```bash
cd OT-ETL-Challenge
```
<a name="2"></a>
## 2) Download datasets

You have 2 options to get the datasets, manually and using a bash script. After execute you'll find on the "data" directory JSON file format datasets needed to execute the application and logs for each dataset. The directory tree should look like:

```
├── code
├── data
│   ├── evidence
│   │   └── *.json
│   ├── evidence.log
│   ├── targets
│   │   └── *.json
│   ├── targets.log
│   ├── diseases
│   │   └── *.json
│   └── diseases.log
├── test
├── download_datasets.sh
├── LICENCE
└── main.py
└── README.md
└── requirmenet.txt
```

Download datasets could take beetween 25 to 45 minutes depending on the internet speed.

### 2 options:

**Option 1: Manually**

Create directories. Execute:
```bash
data_dir=data
mkdir -p $data_dir/{evidence,targets,diseases}
```

Get datasets. Execute:
```bash
wget --no-parent --level=1 --no-directories --directory-prefix=$data_dir/evidence --accept='*.json' -r ftp://ftp.ebi.ac.uk/pub/databases/opentargets/platform/21.11/output/etl/json/evidence/sourceId=eva/ -o $data_dir/evidence.log
wget --no-parent --level=1 --no-directories --directory-prefix=$data_dir/targets --accept='*.json' -r ftp://ftp.ebi.ac.uk/pub/databases/opentargets/platform/21.11/output/etl/json/targets/ -o $data_dir/targets.log
wget --no-parent --level=1 --no-directories --directory-prefix=$data_dir/diseases --accept='*.json' -r ftp://ftp.ebi.ac.uk/pub/databases/opentargets/platform/21.11/output/etl/json/diseases/ -o $data_dir/diseases.log
```

**Option 2: Using script**

The execution will be a background paralell process for each dataset(faster than manual option).

Add permisions. Execute:
```bash
chmod 700 download_datasets.sh
```

Run script. Execute:
```bash
./download_datasets.sh
```

#### Check logs

In the logs you can check if all the files are sucessfully downloaded and if the execution is finished.

To check if download datasets finish. On "data" folder execute:
```bash
grep -e "FINISHED" evidence.log targets.log diseases.log
```

Check for download errors. On "data" folder execute:
```bash
grep -e "error" -e "fail" evidence.log targets.log diseases.log | awk -F: '{print "Line "$1": "$2}'
```
<a name="3"></a>
## 3) Setting up environment

On "OT-ETL-Challenge" folder. Execute:

1) Create virtual environment. Execute:
```bash
python -m venv etl
```

2) Activate virtual environment. Execute:
```bash
source etl/bin/activate
```

3) Install python dependencies. Execute:
```bash
pip install -r requirement.txt
```
<a name="4"></a>
## 4) Running application

Usage options:
| Flag       | Description                                 | Default  |
|:-----------:|:-------------------------------------------:|:---------:|
| "-in"      | Datasets directory                          | "data" |
| "-out"     | Result JSON file output name                 | "result.json" |
| "-n"       | Numbers of CPUs used to parse datasets           | 1 |   
| "-nc"      | Get the number of CPUs available             |   |
| "-elt"     | Run ETL pipeline to get JSON file result     |   |
| "-tt"      | Count how many target-target pairs share a connection to at least two diseases |   |

Get the result JSON file using 8 CPUs. Execute:
```bash
python main.py -etl -n 8
```

Count how many target-target pairs share a connection to at least two diseases using 1 CPUs. Execute:
```bash
python main.py -tt
```

Get how many CPUs are available on your execution environment. Execute:
```bash
python main.py -nc
```

Show usage options. Execute:
```bash
python main.py -h
```

##### Notes
* If you select more CPUs than you execution environment has the application takes the max possible.
* You can run both -etl and -tt options together.
* If another directory is selected(-in) keep "data" folder distribution by evidence, target and diseases.


## 5) Testing

Execute on "test" directory. There are 3 types of tests:
* Test if the datasets used as input have format and quality expected to run correctly the application.

Execute:
```bash
python test_metadata.py -v
```

* Test the time performance of -etl(transform_data), -tt(target_target_pair) and export options of the application. Iterates trought each number of processors until the max CPU available measuring the time spend. From each CPU iteration it evaluates the code 3 times (3 laps) and calculate the mean time of them. Finally you'll get a summary of the execution time spend based on the number of CPUs used.

Execute:
```bash
python test_performance.py
```

* Test data trasnformation and calculatations using mock dataset (Mini dataset is beneficial to debug expected behaviours).

Execute:
```bash
python test_transform.py -v
```

## Challenge results
* "result.json" file contains the results request on the tech test instructions document.
* The number of target-target pair sharing connection with at least two diseases is 350414.
* You can get both results running the application option -etl and -tt.