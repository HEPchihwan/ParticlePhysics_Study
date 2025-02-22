	Followed 
https://github.com/CMSSNU/SNU-project/tree/main/4.SNU-CMS/Tutorials/MachineLearning

1. miniconda 설치하기
```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
# minicodna 다운 
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
# 실행 

```
2. env setting 
```bash 
source ~/miniconda3/bin/activate
conda config --set channel_priority strict
conda create -c conda-forge --name $YOUR_ENVIRONMENT_NAME root python=3.11
conda activate $YOUR_ENVIRONMENT_NAME
pip install torch --index-url https://download.pytorch.org/whl/cpu
pip install torch_geometric
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.1.0+cpu.html
pip install numpy pandas matplotlib scikit-learn seaborn tensorboard
pip install ipykernel jupyter
pip install captum
```
3. 예제 파일 및 데이터 파일 설정 
```bash
#예제파일 -> git에 있는 snututorial 4. machine learning  , notebook

# 데이터파일 -> 
cp -r /data9/Users/choij_public/MachineLearning/DATA $YOUR_TUTORIAL_DIRECTORY/DATA
# e.g.
# cp -r /data9/Users/choij_public/MachineLearning /data9/Users/choij/SNU-project/4.SNU-CMS/Tutorials/MachineLearning/DATA

```

 > 	[[MachineLearning_SNUtutorial|예제별 설명]]
