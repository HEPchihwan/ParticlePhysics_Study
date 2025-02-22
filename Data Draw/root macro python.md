###   Read txt & draw histogram 
``` python
import numpy as np
import matplotlib.pyplot as plt

# 데이터 파일 읽기

data = np.loadtxt('output/alp_time_data.txt')

# 히스토그램 그리기

plt.hist(data, bins=50, alpha=0.75, color='blue', edgecolor='black')

# 그래프 제목과 레이블 설정

plt.title('Distribution of Alp Time Data')

plt.xlabel('Value')

plt.ylabel('Frequency')

# 그래프 저장하기

plt.grid(True)

plt.savefig('output/alp_time_histogram.png') # 그래프를 PNG 파일로 저장

plt.close() # 그래프 창 닫기
```
### Heat map
>[!note]
> *WR을 기준으로 파일을 나누고 해당 파일을 가져와 병렬로 해당 파일들을 다 가져와 하나씩 결과를 내놓음.*
> 1. 	def process_file(file_name): 
>	with uproot.open(file_name) as file:
>	MET_hist = file["Not triggered"]
>	MET_hist_value = MET_hist.values()[0]
>    
>    을 통해 각 파일에 해당하는 데이터를 뽑아내고 연산하는 과정의 함수를 작성 
>    이런식으로 뽑아옴
>    
> 2. 	with ProcessPoolExecutor() as executor: futures = {executor.submit(process_file, file_name): file_name for file_name in all_file_names}
>   을 통해 filname 안에 있는 여러 파일들을 알아서 가져와서 병렬로 처리해줌 
>3. 	이후 2D matrix 만드는데 가져온 WR N 데이터 정렬 후 이걸 res 라는 변수에 담음 
>      그리고 *for res in results: * 를 이용해  results 안에 WR 1000 N100 , N200... 안의  데이터가 들어있는데 매 순회마다 이걸 각각 res 라는 변수에 부여하는 과정을 함.


```python
import os
import numpy as np
import matplotlib.pyplot as plt
import uproot
from concurrent.futures import ProcessPoolExecutor, as_completed

  

def process_file(file_name):

	try:

		with uproot.open(file_name) as file:

		# "Cutflow" 및 "Not triggered/Cutflow" 히스토그램 가져오기

		MET_hist = file["Not triggered"]

		mutau_hist = file["mu_tau"]

		tau_hist = file["tau"]

		# taucut 및 mutaucut 값 추출 (히스토그램의 bin 인덱스 확인 필요)
	
		MET_hist_value = MET_hist.values()[0]

		mutauPTcutvalue = mutau_hist.values()[2]

  

		tauPTcutvalue = tau_hist.values()[2]

		# 비율 계산

		if MET_hist_value != 0:

			taucutratio = tauPTcutvalue / MET_hist_value

			mutaucutratio = mutauPTcutvalue / MET_hist_value

			compareratio = (mutauPTcutvalue - tauPTcutvalue)/ tauPTcutvalue

		else:

			print ("Err")
			
			taucutratio= 0
			
			mutaucutratio = 0
			
			compareratio = 0
			# WR과 N 값 추출 (파일명에서 WR과 N 값을 파싱)
			parts = file_name.split('_')
		
			WR_part = parts[3] # 예: "WR1000"
		
			N_part = parts[4] # 예: "N100.root"
		
			WR = int(WR_part.replace('WR', ''))
		
			N = int(N_part.replace('N', '').replace('.root', ''))
		
			return (WR, N, taucutratio, mutaucutratio, compareratio)

	except FileNotFoundError:

		print(f"파일을 찾을 수 없습니다: {file_name}")

	except KeyError as e:

		print(f"히스토그램을 찾을 수 없습니다: {file_name}, {e}")

	except Exception as e:

		print(f"파일 처리 중 오류 발생: {file_name}, {e}")

	return None

def plot_heatmaps():

	# 파일 이름 목록 정의
	fileNames1000 = [

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N100.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N200.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N300.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N400.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N500.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N600.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N700.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N800.root",

		"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N900.root",
	]

	# 모든 파일 리스트를 하나로 결합

	all_file_names = (fileNames1000 + fileNames1500 + fileNames2000 +
		fileNames2500 + fileNames3000 + fileNames3500 +
		fileNames4000 + fileNames4500 + fileNames5000 +
		fileNames5500 + fileNames6000 + fileNames6500 )
	# 출력 디렉토리 생성

	output_dir = 'output'

	if not os.path.exists(output_dir):
		os.makedirs(output_dir)

	# 히스토그램 데이터를 저장할 리스트

	results = []

	# 병렬 처리 설정

	with ProcessPoolExecutor() as executor:

		futures = {executor.submit(process_file, file_name): file_name for               file_name in all_file_names}

	for future in as_completed(futures):

		result = future.result()

	if result:

		results.append(result)

	if not results:

		print("유효한 데이터가 없습니다. 스크립트를 종료합니다.")
		exit()

	# WR과 N 값에 따라 데이터 정렬 및 2D 배열 생성

	WR_values = sorted(list(set([res[0] for res in results])))

	N_values = sorted(list(set([res[1] for res in results])))

	# 인덱스 매핑

	WR_to_index = {WR: idx for idx, WR in enumerate(WR_values)}

	N_to_index = {N: idx for idx, N in enumerate(N_values)}


	# 2D 배열 초기화

	taucut_ratios_matrix = np.zeros((len(WR_values), len(N_values)))

	mutaucut_ratios_matrix = np.zeros((len(WR_values), len(N_values)))

	ratio_matrix = np.zeros((len(WR_values), len(N_values)))


	for res in results:

		WR, N, taucutratio, mutaucutratio , compareratio = res

		i = WR_to_index[WR]

		j = N_to_index[N]

		taucut_ratios_matrix[i, j] = taucutratio

		mutaucut_ratios_matrix[i, j] = mutaucutratio

		ratio_matrix[i, j] = compareratio

	# 히트맵 그리기 함수

	def draw_heatmap(data, title, filename, cmap='viridis'):

		plt.figure(figsize=(15, 10))

		plt.imshow(data, aspect='auto', cmap=cmap, origin='lower',

		extent=[N_values[0]-50, N_values[-1]+50, WR_values[0]-250,                       WR_values[-1]+250])

		plt.colorbar(label='Ratio')

		plt.xticks(ticks=N_values, labels=[f"N{N}" for N in N_values],                   rotation=90)

		plt.yticks(ticks=WR_values, labels=[f"WR{WR}" for WR in WR_values])

		plt.title(title)

		plt.xlabel('N Value')

		plt.ylabel('WR Energy (GeV)')

		plt.tight_layout()

		plt.savefig(os.path.join(output_dir, filename))

		plt.show()

  

	# 첫 번째 히트맵: taucut_ratios

	draw_heatmap(taucut_ratios_matrix,'Heatmap of taucut                             Ratios','heatmap_taucut_ratios.png', cmap='viridis')
	
	# 두 번째 히트맵: mutaucut_ratios

	draw_heatmap( mutaucut_ratios_matrix,'Heatmap of mutaucut                        Ratios','heatmap_mutaucut_ratios.png',cmap='plasma')

	# 세 번째 히트맵: mutaucut_ratios / taucut_ratios

	draw_heatmap(ratio_matrix,'Heatmap of taucutratio & mu or taucut Ratios',
	'heatmap_ratio.png',cmap='coolwarm')

if __name__ == "__main__":
	plot_heatmaps()
```

### 참고사항 
>[!note] try 변수
```c
try:
    # 실행하려는 코드
except ExceptionType as e:
    # 오류가 발생했을 때 처리할 코드
else:
    # 오류가 발생하지 않았을 때 실행할 코드 (선택적)
finally:
    # 오류 발생 여부와 상관없이 항상 실행할 코드 (선택적)
```

>[!note] if  __name__ == "__main__":
```python
if __name__ == "__main__":
	# 이 파일을 직접 실행하는 경우만 이걸 실행할 수 있도록 설정함 .
	plot_heatmaps()
```
### Root 파일에서 값을 가져와서 다른 루트파일에 값 넣기
```python
import glob

from ROOT import *

  

# 디렉토리 경로

directory = "/data6/Users/snuintern1/nano3/CMSSW_10_6_22/src/PhysicsTools/NanoAODTools/python/postprocessing/analyzer/analyzer/Genlevel_selection/output"

file_list = glob.glob(f"{directory}/*.root")

  

# 출력 ROOT 파일 생성

output_file = TFile("merged_results.root", "RECREATE")

  

# 히스토그램 병합을 위한 빈 히스토그램 생성

combined_AK8 = TH1F("combined_AK8", "Combined AK8 Mass", 100, 0, 1000)

combined_AK4_el = TH1F("combined_AK4_el", "Combined AK4 Mass (Electron)", 100, 0, 1000)

combined_AK4_mu = TH1F("combined_AK4_mu", "Combined AK4 Mass (Muon)", 100, 0, 1000)

combined_subAK8_el = TH1F("combined_subAK8_el", "Combined SubAK8 Mass (Electron)", 100, 0, 1000)

combined_subAK8_mu = TH1F("combined_subAK8_mu", "Combined SubAK8 Mass (Muon)", 100, 0, 1000)

  

# 히스토그램 색상 설정

combined_AK4_el.SetLineColor(kRed) # Electron은 빨간색

combined_AK4_mu.SetLineColor(kBlue) # Muon은 파란색

combined_subAK8_el.SetLineColor(kRed) # Electron은 빨간색

combined_subAK8_mu.SetLineColor(kBlue) # Muon은 파란색

# 병합 히스토그램 생성

combined_AK4 = THStack("combined_AK4", "Combined AK4 Mass; Mass (GeV); Events")

combined_subAK8 = THStack("combined_subAK8", "Combined SubAK8 Mass; Mass (GeV); Events")

  

# 각 파일을 읽고 히스토그램 병합

for files in file_list:

print(f"Processing file: {files}")

file_plots = TFile.Open(files) # ROOT 파일 열기

plots_dir = file_plots.Get("plots") # "plots" 디렉토리 가져오기

  

# 각 히스토그램 가져오기

AK8 = plots_dir.Get("tau_AK8_cut_mass")

AK4_el = plots_dir.Get("tau_AK4_cut_mass_electron")

AK4_mu = plots_dir.Get("tau_AK4_cut_mass_muon")

subAK8_el = plots_dir.Get("tau_subAK8_cut_mass_electron")

subAK8_mu = plots_dir.Get("tau_subAK8_cut_mass_muon")

  

# 가져온 히스토그램 데이터를 병합 히스토그램에 추가

if AK8:

combined_AK8.Add(AK8)

if AK4_el:

combined_AK4_el.Add(AK4_el)

if AK4_mu:

combined_AK4_mu.Add(AK4_mu)

if subAK8_el:

combined_subAK8_el.Add(subAK8_el)

if subAK8_mu:

combined_subAK8_mu.Add(subAK8_mu)

  

file_plots.Close()

# 병합된 히스토그램을 THStack에 추가

combined_AK4.Add(combined_AK4_el)

combined_AK4.Add(combined_AK4_mu)

combined_subAK8.Add(combined_subAK8_el)

combined_subAK8.Add(combined_subAK8_mu)

# 병합된 히스토그램을 출력 ROOT 파일에 저장

output_file.cd()

combined_AK8.Write()

combined_AK4.Write()

combined_subAK8.Write()

output_file.Close()

  

print("combinedograms merged and saved to 'merged_results.root'.")
```