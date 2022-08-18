## 1. Data
### (1) Raw data files
The uploaded files including the 3-week raw data of 10 large-scale application running in the data center (i.e. app0~app9). All the data can be found at folder: "data/data_submit/", which consists of 10 .csv files.

### (2) Train/Test Dataset Example
We choose the aplication app8, which is a front-end appliation for the customers. In general, we construct the example dataset at folder: "data/data_train_test/app8-example/". The ".npy" file in "data/data_train_test/app8-example/" save the train/test dataset sample.
Specifically, we construct: 1) train dataset for 12-hour QPS sequence (720 items) and 2) test dataset for 2-hour QPS sequence (120 items). We list the dataset files as follow:

 1) "*_train/test_prophet_ts.npy": trend \& dominant seasonality components in the train/test dataset, e.g., "app8-example_train/test_prophet_ts.npy"
 2) "*_train/test_ts.npy": predictable residual components to train/test the main Informer architectures,e.g. "app8-example_train/test_ts.npy"
 3) "*_train/test_event.npy": predictable residual components to train/test the ER-embedding Informer architectures,e.g., "app8-example_train/test_event.npy"; 
 4) "*\_train/test\_base\_ts.npy": the ground-truth of the raw data for train/test datasets
 
 ## 2. Code
 ### (1) Requirements
 You need to run our experiments with ``python 3.8``
 The packages you need to install are listed as follow:
 #### matplotlib==3.1.1
#### numpy==1.23.1
#### pandas==1.4.3
#### scikit_learn==0.24.2
#### torch==1.8.0
#### prophet==1.1
#### dtaidistance==2.3.9
#### statsmodels==0.12.2
#### scipy==1.8.1

 ### (2) Code architecture
 In our supplementary material, all the code are in the folder ``KAE-Informer``. Including the folder ``model`` to save the code of modeling the KAE-Informer; and the folder ``util`` and ``exp`` to save the code about some tool function and the code which are called to execute the experiments.
 
 ### (3) Reproducing the Experiments
 
 To run the experiments, firstly, you need to run the file: ``data_event_search_ts_extractor.py`` to construct the train/test datasets as:
 
 python data_event_search_ts_extractor.py --app=app8 --start_day=18 --end_day=-1 --test_len=1440 --input_len=1440 --predict_len=60 --event_num=7 --dtw_length=240 --freq=min --sample_rate=1440
 
 This command can generate the train dataset with 2820 items and a test set with 1440 items, the meaning of each paramter is:
 #### --app: selected app
 #### --test_len: length of the total predicted items
 #### --start_day: the day start to construct the dataset
 #### --end_day: the day ends to process the dataset, -1 means to the end of the data (do not change it)
 #### --predict_len: the steps of $Y_L$ which is predicted by the Informer-architecture at once
 #### --input_len: the steps of $X_L$ which is inputed to the main Informer architecture
 #### --event_num: the number of similar events queried from the historical data
 #### --dtw_length: the length of the sequence to input to the dtw algorithm to query the similar event
 #### --freq: the freq of the raw data,default min for 1-minute sampling interval
 #### --sample_rate: the sampling rate,i.e., the number of the sampling points collected per day
 
 Secondly, you can run the experiments by running the ``main-informer.py`` as:
 
 python main-informer.py --gpu=0 --model=kaeinformer --app=app8-example --d_model=512 --d_ff=2048 --data=qps --d_model=512 --d_ff=2048 --data_len=720 --data_type=0 --seq_len=1440 --label_len=240 --train_epochs=20 --patience=6 --loss=huber --loss_beta=0.2 --qvk_kernel_size=5 --event_qvk_kernel_size=3 --sample_type=0 --root_path=./data/data_train_test/  --do_predict --checkpoints=CEA/checkpoints/
 
 #### --app: selected app
 #### --model: model type (default:kaeinformer)
 #### --d_model: length of the total predicted items
 #### --start_day: the day start to construct the dataset
 #### --end_day: the day ends to process the dataset, -1 means to the end of the data (do not change it)
 #### --predict_len: the steps of $Y_L$ which is predicted by the Informer-architecture at once
 #### --input_len: the steps of $X_L$ which is inputed to the main Informer architecture
 #### --event_num: the number of similar events queried from the historical data
 #### --dtw_length: the length of the sequence to input to the dtw algorithm to query the similar event
 #### --freq: the freq of the raw data,default min for 1-minute sampling interval
 #### --sample_rate: the sampling rate,i.e., the number of the sampling points collected per day
 
