
### Root Tree 안의 데이터를 그대로 가져올때 


#### Get txt file
>[!note]
>- tree->SetBranchAddress("ALPpair_time", &alp_time); 
>- for (int i = 0; i < tree->GetEntries(); ++i) { // 해당 데이터 수 보다 적을 때 
>	tree->GetEntry(i);



```c
#include <iostream>

#include <fstream>

void rootmacro2_extract_alp_time() {

	TFile *file = TFile::Open("stage_output.root"); // 파일 열기
 
	TTree *tree = (TTree*)file->Get("ALPpair"); // 파일의 트리 열기 

	float alp_time;

	tree->SetBranchAddress("ALPpair_time", &alp_time); 

  

	// 특정 폴더에 파일 저장

	std::ofstream outfile("output/alp_time_data.txt");

  

	for (int i = 0; i < tree->GetEntries(); ++i) { // 해당 데이터 수 보다 적을 때 

		tree->GetEntry(i);

		outfile << alp_time << std::endl;
	}
	outfile.close();

	file->Close();
}
```


#### Draw histogram
>[!note]
>- TH1F *hist1_gamma = new TH1F("히스토그램의 이름", "히스토그램 제목; x축 라벨 ;  y축라벨", 빈 수 , 최소값, 최대값);
>- TCanvas *canvas1 = new TCanvas("캔버스 이름", "캔버스 제목", 1600, 1200);
>- latex1->DrawLatex(0.2, 0.8, Form("Above 20: %f", hist1_gamma-                    >Integral(hist1_gamma->FindBin(20) , hist1_gamma->GetNbinsX())));
>( hist1_gamma->Integral( 적분 시작 구간, 적분 마지막 구간 (전체구간)))


```c
#include <cmath> // 수학 함수 사용을 위해 포함


// 빈의 개수를 자동으로 계산해주는 함수

int CalculateOptimalBins(int nEntries) {
	if (nEntries <= 0) return 10; // 데이터가 없는 경우 기본 값 반환
	return std::max(5, static_cast<int>(sqrt(nEntries))); // 최소 5개의 빈을 유.     지,sqrt(N) 반환
}

void extract_alp_time() {

	TFile *file = TFile::Open("stage_output.root");

	TTree *tree = (TTree*)file->Get("ALPpair"); 

	float alp_time;

	tree->SetBranchAddress("ALPpair_time", &alp_time);

	std::ofstream outfile("alp_time_data.txt");

	for (int i = 0; i < tree->GetEntries(); ++i) {
		tree->GetEntry(i);
		outfile << alp_time << std::endl;
		outfile.close();
		file->Close();
	}

}

void rootmacro() {

	// ROOT 파일 열기

	TFile *file = TFile::Open("stage_output.root");

	if (!file || file->IsZombie()) {

		std::cerr << "Error: Cannot open the file!" << std::endl;

		return;

	}

	// Stage1 트리 가져오기 

	TTree *tree1 = (TTree*)file->Get("Stage1"); // Stage1 트리 이름 

	// stage1의 데이터 전부 다 가져오기 
	int nEntries_gamma_stage1 = tree1->GetEntries("gamma_Energy != 0");

	int nEntries_electron_stage1 = tree1->GetEntries("electron_Energy != 0");

	int nEntries_neutron_stage1 = tree1->GetEntries("neutron_Energy != 0");


	// 히스토그램 빈의 개수 자동 계산

	int bins_gamma_stage1 = CalculateOptimalBins(nEntries_gamma_stage1);

	int bins_electron_stage1 = CalculateOptimalBins(nEntries_electron_stage1);

	int bins_neutron_stage1 = CalculateOptimalBins(nEntries_neutron_stage1);


	// Stage1 브랜치에 대한 히스토그램 생성 및 데이터 필터링

	TH1F *hist1_gamma = new TH1F("hist1_gamma", "Stage1 Gamma Energy;Energy          (MeV);Number of Particles", bins_gamma_stage1, 0, 20);

	TH1F *hist1_electron = new TH1F("hist1_electron", "Stage1 Electron               Energy;Energy (MeV);Number of Particles", 50, 0, 20);

	TH1F *hist1_neutron = new TH1F("hist1_neutron", "Stage1 Neutron                  Energy;Energy (MeV);Number of Particles", 200, 0, 20);


	// 캔버스 생성 및 그래프 그리기

	TCanvas *canvas1 = new TCanvas("canvas1", "Stage1 Branches", 1600, 1200);

	canvas1->Divide(2, 2); // 캔버스를 2x2로 분할

	canvas1->cd(1);

	hist1_gamma->SetLineColor(kBlue);

	hist1_gamma->Draw();

	TLatex *latex1 = new TLatex(); // 문구 삽입 

	latex1->SetNDC(); // Normalized device coordinates

	latex1->SetTextSize(0.04);

	latex1->DrawLatex(0.2, 0.8, Form("Above 20: %f", hist1_gamma-                    >Integral(hist1_gamma->FindBin(20), hist1_gamma->GetNbinsX())));

  

	canvas1->cd(2);

	hist1_electron->SetLineColor(kRed);

	hist1_electron->Draw();

	TLatex *latex2 = new TLatex();

	latex2->SetNDC();

	latex2->SetTextSize(0.04);

	latex2->DrawLatex(0.2, 0.8, Form("Above 20: %f", hist1_electron-                 >Integral(hist1_electron->FindBin(20), hist1_electron->GetNbinsX())));

  

	canvas1->cd(3);

	hist1_neutron->SetLineColor(kGreen);

	hist1_neutron->Draw();

	TLatex *latex3 = new TLatex();

	latex3->SetNDC();

	latex3->SetTextSize(0.04);

	latex3->DrawLatex(0.2, 0.8, Form("Above 20: %f", hist1_neutron-                  >Integral(hist1_neutron->FindBin(20), hist1_neutron->GetNbinsX())));

  

	canvas1->cd(4);

	hist1_gamma->SetLineColor(kBlue);

	hist1_gamma->Draw();

	hist1_electron->SetLineColor(kRed);

	hist1_electron->Draw("same");

	hist1_neutron->SetLineColor(kGreen);

	hist1_neutron->Draw("same");

	// 이미지로 저장

	canvas1->SaveAs("output/stage1_branch_histograms.png");


	// 파일 닫기
	file->Close();
}

```


### Several root file 
>[!note] 
>1. 파일이름 목록 설정 후 array로 설정 
>2. array들을 loop 돌리고 
>TH1F hist_frame6 = new TH1F  설정하기  ( 히스토그램 준비하기 )
>3. for (const auto &fileName  fileNames) {
TFile *file = TFile::Open(fileName.c_str()); 으로 WR 그룹안에 있는 모든 파일들을 iteration하면서 각 순회 마다 값을 가져옴 
> 4. 각 순회마다 해당 histframe에 자기 bin에 해당하는 값을 삽입 후 다음 N200 으로 넘어가서 이 걸 반복하고 WR 그룹에 대해 이게 끝나면 히스토그램 하나 끝남.


```c
#include <TFile.h>

#include <TH1F.h>

#include <TCanvas.h>

#include <iostream>

#include <vector>

#include <cmath>

#include <TStyle.h> // gStyle 사용을 위해 추가

  

void macro() {

	gStyle->SetOptStat(0); // 통계박스 비활성화

	// 1. 파일 이름 목록 정의

	std::vector<std::string> fileNames1000 = {

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N100.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N200.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N300.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N400.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N500.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N600.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N700.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N800.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1000_N900.root"};

	std::vector<std::string> fileNames1500 = {

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N100.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N1000.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N1100.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N1200.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N1300.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N1400.root",

	"Wtau_test_WRtoNTautoTauTauJJ_WR1500_N200.root",
	};  
	////// ... 


	std::vector<std::string> *fileNamesArray[] = {&fileNames1000,                    &fileNames1500, &fileNames2000 , &fileNames2500, &fileNames3000,                 &fileNames3500, &fileNames4000, &fileNames4500, &fileNames5000,                  &fileNames5500, &fileNames6000, &fileNames6500};

  

	// 각 파일 이름 목록에 대해 반복 처리

	for (int i = 0; i < sizeof(fileNamesArray)/sizeof(fileNamesArray[0]); ++i) {

		auto fileNames = fileNamesArray[i];

		int numFiles = fileNames->size(); // 루트파일 개수 계산

		int mass = 1000 + i * 500; // mass = 1000, 1500, 2000, ..., 6500

		int k = mass; // k를 mass로 설정 ( 파일들의 이름을 사용하기 위해 설정  )

  

		// MET 통과 + tigger통과 + ID 통과 + Pt cut 통과

  

		TH1F *hist_frame5 = new TH1F("hist_frame5", "Ratio of taucut + IDcut +           Ptcut", numFiles + 10, 0, numFiles + 10);

		hist_frame5->GetXaxis()->SetTitle(Form("~N%d (GeV)", k));

		hist_frame5->GetYaxis()->SetTitle("taucut + IDcut + Ptcut");

  

		TH1F *hist_frame6 = new TH1F("hist_frame6", "Ratio of mutaucut + IDcut +         Ptcut", numFiles + 10, 0, numFiles + 10);

		hist_frame6->GetXaxis()->SetTitle(Form("~N%d (GeV)", k));

		hist_frame6->GetYaxis()->SetTitle("mutaucut + IDcut + Ptcut");

  

		TH1F *hist_frame7 = new TH1F("hist_frame7", "Ratio trigger of taucut +           IDcut + Ptcut", numFiles + 10, 0, numFiles + 10);

		hist_frame7->GetXaxis()->SetTitle(Form("~N%d (GeV)", k));

		hist_frame7->GetYaxis()->SetTitle("trigger of taucut + IDcut + Ptcut");

  
  

		double scale_factor = 1.5; // 최댓값을 20% 정도 더 크게 설정
		
		hist_frame5->SetMaximum(hist_frame5->GetMaximum() * scale_factor);

		hist_frame6->SetMaximum(hist_frame6->GetMaximum() * scale_factor);

  

		// X축 레이블 설정

		for (int bin = 1; bin <= numFiles + 10; ++bin) {

			int labelValue = bin * 100; // 레이블 값을 100의 배수로 설정

  

			if (k == 1000) { // k가 1000일 때

				if (bin % 2 == 0) {

					hist_frame5->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame6->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame7->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

				}
			}
			else if (k >= 1000 && k < 2000) {

				if (bin % 2 == 0) {

					hist_frame5->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame6->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame7->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));
				}
			} 
			else if (k >= 2000 && k < 4000) {

				if (bin % 5 == 0) {

					hist_frame5->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame6->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame7->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

				}

			} 
			else if (k >= 4000 && k <= 6500) {

				if (bin % 10 == 0) {

					hist_frame5->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame6->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));

					hist_frame7->GetXaxis()->SetBinLabel(bin, Form("%d",                             labelValue));
				}
			}
		}

  

		// 기존 레이블 간격을 유지하면서 100배로 설정

		//int mass = (i + 1) * 500 + 500; // 1000, 1500, 2000, ..., 6500

		int binIndex = 1; // 히스토그램의 현재 빈 인덱스

		// 3. 파일 목록 반복 처리

		for (const auto &fileName : *fileNames) {

			TFile *file = TFile::Open(fileName.c_str());

				if (!file || file->IsZombie()) {

					std::cerr << "Failed to open file: " << fileName <<                              std::endl;
					continue;
				}

			// 4. cutflow 히스토그램 가져오기

			TH1 *cutflowHist = (TH1*)file->Get("Not triggered");

			if (!cutflowHist) {

				std::cerr << "Failed to find cutflow in file: " << fileName <<                   std::endl;

				file->Close()
				continue;
			}

			// 5. mutau히스토그램 가져오기

			TH1 *mutautriggerHist = (TH1*)file->Get("mu_tau");

			if (!mutautriggerHist) {

				std::cerr << "Failed to find Not mu_tau in file: " << fileName                   << std::endl;

				file->Close();

				continue;
			}

  

			// 5. tau히스토그램 가져오기

			TH1 *tautriggerHist = (TH1*)file->Get("tau");

			if (!tautriggerHist) {

				std::cerr << "Failed to find Not tau in file: " << fileName <<                   std::endl;
				file->Close();
				continue;
			}

  

			// 6. 각 파일의 taucut 및 NotTriggeredCutflow 값 추출

			double METcut = cutflowHist->GetBinContent(1);

			double tautriggercut = tautriggerHist->GetBinContent(1);

			double tautIDcut = tautriggerHist->GetBinContent(2);

			double tauPTcut = tautriggerHist->GetBinContent(3);

			double mutautriggercut = mutautriggerHist->GetBinContent(1);

			double mutauIDcut = mutautriggerHist->GetBinContent(2);

			double mutauPTcut = mutautriggerHist->GetBinContent(3);

  
  

			// 7. taucut / notTriggeredCutflow 비율 계산 및 히스토그램에 채우기

			if (METcut != 0) {

				double tautriggercutratio = tautriggercut / METcut;

				double mutautriggercutratio = mutautriggercut / METcut;

				double tauIDcutratio = tautIDcut / METcut;

				double mutauIDcutratio = mutauIDcut / METcut;

				double tauPTcutratio = tauPTcut / METcut;

				double mutauPTcutratio = mutauPTcut / METcut;

				double final_trigger_ratio = (mutauPTcut - tauPTcut)/tauPTcut ;

				hist_frame5->SetBinContent(binIndex, tauPTcutratio);

				hist_frame6->SetBinContent(binIndex, mutauPTcutratio);

				hist_frame7->SetBinContent(binIndex, final_trigger_ratio);

			} 
			else {

				std::cerr << "Warning: NotTriggeredCutflow is zero in file: " <<                 fileName << std::endl;

			}

  

			binIndex++; // 다음 빈으로 이동

			file->Close(); // 파일 
		}


		// 10. 세 번째 액자에 두 히스토그램을 함께 그리기

		TCanvas *canvas3 = new TCanvas(Form("canvas3_WR%d", mass), "Ptcut                ratio", 800, 600);

		hist_frame5->SetLineColor(kRed); // 첫 번째 히스토그램의 색상 설정

		hist_frame6->SetLineColor(kBlue); // 두 번째 히스토그램의 색상 설정

		hist_frame5->Draw(); // 첫 번째 히스토그램 먼저 그리기

		hist_frame6->Draw("same"); // 두 번째 히스토그램을 동일 캔버스에 겹쳐서 그리기

  

		// 범례 추가

		TLegend *legend = new TLegend(0.7, 0.7, 0.9, 0.9); // 범례 위치 설정

		legend->AddEntry(hist_frame5, "taucut ratio", "l");

		legend->AddEntry(hist_frame6, "mutaucut ratio", "l");

		legend->Draw();

  

		canvas3->SaveAs(Form("output/ptcut_WR%d.png", mass)); // 결과 저장

  
  

		TCanvas *canvas4 = new TCanvas(Form("canvas4_WR%d", mass), "compare with         each ratio", 1600, 1200);

		hist_frame4->SetLineColor(kGreen - 4); // 밝은 초록

		hist_frame4->SetLineStyle(2); // dashed line

		hist_frame5->SetLineColor(kOrange + 1); // 진한 주황

		hist_frame5->SetLineStyle(1); // solid line

		hist_frame6->SetLineColor(kOrange - 3); // 밝은 주황

		hist_frame6->SetLineStyle(2); // dashed line

  

		// 히스토그램을 겹쳐서 그림


		hist_frame4->Draw();

		hist_frame5->Draw("same");

		hist_frame6->Draw("same");

  

		// 범례 추가

		TLegend *legend4 = new TLegend(0.7, 0.7, 0.9, 0.9);

		legend4->AddEntry(hist_frame4, "mutaucut + IDcut (dashed light green)",           "l");

		legend4->AddEntry(hist_frame5, "taucut + IDcut + Ptcut (solid dark               orange)", "l");

		legend4->AddEntry(hist_frame6, "mutaucut + IDcut + Ptcut (dashed light           orange)", "l");

		legend4->Draw();

  

		canvas4->SaveAs(Form("output/compare %d.png", mass)); // 결과 저장

		std::cout << "Third frame comparison histogram saved as 'ptcut_ratio_WR"         << mass << ".png'." << std::endl;

  

		TCanvas *canvas5 = new TCanvas(Form("canvas5_WR%d", mass), "compare with         each ratio", 1600, 1200);

		hist_frame7->SetLineColor(kOrange + 1); // 진한 주황

		double fixed_max_value = 0.15; // 원하는 최댓값으로 설정

		hist_frame7->SetMaximum(fixed_max_value); // 최댓값 고정

		hist_frame7->Draw();

  
  

		TLegend *legend7 = new TLegend(0.7, 0.7, 0.9, 0.9); // 범례 위치 설정

		legend7->AddEntry(hist_frame7, "(mutaucut - taucut)/taucut ", "l");

		legend7->Draw();

  

		canvas5->SaveAs(Form("output/twotriggercompare%d.png", mass)); // 결과 저장

  

		std::cout << "Third frame comparison histogram saved as '(mutaucut -             taucut)/taucutoutput/_WR" << mass << ".png'." << std::endl;
	}
}

```

### 참고 

#### 높이 최대치 설정
```c
Double_t maxY = std::max(h_alpPhotonEnergy->GetMaximum(), h_stage2GammaEnergy->GetMaximum());

h_alpPhotonEnergy->SetMaximum(maxY * 1.2); // 최대 높이의 20% 여유 설정
```
