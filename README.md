# Guide-to-Run-QS-Algorithm
A guide to spilt dataset, train the model and inference the model on YDF library using QS algorithm

## Get the train and test data for the model
### Download package information from all configured sources
sudo apt update

### Download HIGGS dataset
sudo apt install wget gzip bzip2 unzip xz-utils<br>
wget https://archive.ics.uci.edu/ml/machine-learning-databases/00280/HIGGS.csv.gz<br>
gzip -d HIGGS.csv.gz

### Give permission to the path and file
chmod + rwx HIGGS.csv<br>
chmod a+rX /every/directory/to/HIGGS.csv<br>

### Set up Postgres
sudo apt install postgresql postgresql-contrib<br>
sudo -i -u postgres<br>
psql<br>
postgres=# \password postgres<br>
Enter new password: <new-password><br>
postgres=# CREATE TABLE higgs(label REAL NOT NULL, leptonpT REAL NOT NULL, leptoneta REAL NOT NULL, leptonphi REAL NOT NULL, missingenergymagnitude REAL NOT NULL, missingenergyphi REAL NOT NULL, jet1pt REAL NOT NULL, jet1eta REAL NOT NULL, jet1phi REAL NOT NULL, jet1btag REAL NOT NULL, jet2pt REAL NOT NULL, jet2eta REAL NOT NULL, jet2phi REAL NOT NULL, jet2btag REAL NOT NULL, jet3pt REAL NOT NULL, jet3eta REAL NOT NULL, jet3phi REAL NOT NULL, jet3btag REAL NOT NULL, jet4pt REAL NOT NULL, jet4eta REAL NOT NULL, jet4phi REAL NOT NULL, jet4btag REAL NOT NULL, mjj REAL NOT NULL, mjjj REAL NOT NULL, mlv REAL NOT NULL, mjlv REAL NOT NULL, mbb REAL NOT NULL, mwbb REAL NOT NULL, mwwbb REAL NOT NULL);<br>
postgres=# /copy higgs from '/path/to/HIGGS.csv' with CSV;<br>

### Install the following module
pip3 install pandas<br>
pip3 install -U scikit-learn<br>

### Spliting the Higgs dataset(under the repository “/netsdb/model-inference/decisionTree/experiments”)
python3 split_data.py<br>
(move HIGGS.csv to the local path before splitting)<br>
  
### Add the headers for training and testing dataset
sed -i -e '1i__LABEL,leptonpT,leptoneta,leptonphi,missingenergymagnitude,missingenergyphi,jet1pt,jet1eta,jet1phi,jet1btag,jet2pt,jet2eta,jet2phi,jet2btag,jet3pt,jet3eta,jet3phi,jet3btag,jet4pt,jet4eta,jet4phi,jet4btag,mjj,mjjj,mlv,mjlv,mbb,mwbb,mwwbb\' HIGGS.csv_train.csv<br>
sed -i -e '1i__LABEL,leptonpT,leptoneta,leptonphi,missingenergymagnitude,missingenergyphi,jet1pt,jet1eta,jet1phi,jet1btag,jet2pt,jet2eta,jet2phi,jet2btag,jet3pt,jet3eta,jet3phi,jet3btag,jet4pt,jet4eta,jet4phi,jet4btag,mjj,mjjj,mlv,mjlv,mbb,mwbb,mwwbb\' HIGGS.csv_test.csv<br>
  
## Set up the yggdrasil library
### GCC installation
sudo apt install build-essential<br>
gcc --version<br>
  
### Make sure Python version >= 3 and < 3.10
python3 --version<br>
  
### Git installation
sudo apt install git-all<br>

### Add Bazel distribution URI as a package source
sudo apt install apt-transport-https curl gnupg<br>
curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > bazel.gpg<br>
sudo mv bazel.gpg /etc/apt/trusted.gpg.d/<br>
echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list<br>
  
### Install and update Bazel
sudo apt update && sudo apt install bazel-3.7.2<br>
  
### Make sure bazel is the right version
sudo apt install bazel-bootstrap<br>
bazel --version<br>
  
### Install numpy
sudo apt install python3-pip<br>
pip3 install numpy<br>
  
### Clone the Yggdrasil github repository
git clone https://github.com/google/yggdrasil-decision-forests.git<br>
cd yggdrasil-decision-forests<br>
  
### Compile command-line-interface
bazel build //yggdrasil_decision_forests/cli/...:all --config=linux_cpp17 --config=linux_avx2<br>
  
### Generate the dataspec for the training dataspec
infer_dataspec --dataset=”csv:/path/to/HIGGS.csv_train.csv” --output=”/path/to/dataspec.pbtxt” --guide=”/path/to/the/existing/guide.pbtxt” --alsologtostderr
  
### Train the model
train --dataset=”csv:/path/to/HIGGS.csv_train.csv” --dataspec=”/path/to/dataspec.pbtxt” --config=”/path/to/the/existing/train_config.pbtxt” --output=”/path/to/model_directory_name”
  
### Benchmark the inference speed of the model
predict --dataset=”csv:/path/to/HIGGS.csv_test.csv” --model=”/path/to/model_directory_name” --output=”/path/to/result_name.txt” (--batch_size=?) (--num_runs=?) --alsologtostderr
  
## Others
### Change the number of trees(line 6)
vim train_config.pbtxt






