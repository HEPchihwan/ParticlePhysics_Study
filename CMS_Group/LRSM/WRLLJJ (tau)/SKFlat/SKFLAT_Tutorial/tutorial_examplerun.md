# ExampleRun 분석 가이드

## 1. 초기화 설정(initializeAnalyzer)

### 시스템 플래그 설정
- [[RunSyst]] 확인 (측정값의 체계적 불확실성을 평가하는 방법)
```c
RunSyst = HasFlag("RunSyst");
```
- [[RunNewPDF]] 확인 (Parton Distribution Function, 양성자 내부의 쿼크와 글루온 분포를 기술하는 함수의 불확실성 분석 ) 
```c
RunNewPDF = HasFlag("RunNewPDF");
```
- [[RunXSecSyst]] 확인 (입자 충돌 반응의 단면적 불확실성 분석)
```c
RunXSecSyst = HasFlag("RunXSecSyst");
```

### 뮤온 ID 설정
```cpp
MuonIDs = {
  "POGMedium",
  "POGTight" 
}
```

### 연도별 트리거 설정
| 연도 | 트리거명 | PT 컷 |
|------|----------|--------|
| 2016 | HLT_IsoMu24_v | > 26 |
| 2017 | HLT_IsoMu27_v | > 29 |

### B tagging & PDF 설정

```cpp
	std::vector<JetTagging::Parameters> jtps;
	  jtps.push_back( JetTagging::Parameters(JetTagging::DeepCSV,     JetTagging::Medium, JetTagging::incl, JetTagging::comb) );
  mcCorr->SetJetTaggingParameters(jtps);
```

```cpp
RunNewPDF = HasFlag("RunNewPDF");
  cout << "[ExampleRun::initializeAnalyzer] RunNewPDF = " << RunNewPDF << endl;
  if(RunNewPDF && !IsDATA){

    LHAPDFHandler LHAPDFHandler_Prod;
    LHAPDFHandler_Prod.CentralPDFName = "NNPDF31_nnlo_hessian_pdfas";
    LHAPDFHandler_Prod.init();

    LHAPDFHandler LHAPDFHandler_New;
    LHAPDFHandler_New.CentralPDFName = "NNPDF31_nlo_hessian_pdfas";
    LHAPDFHandler_New.ErrorSetMember_Start = 1; 
    LHAPDFHandler_New.ErrorSetMember_End = 100; 
    LHAPDFHandler_New.AlphaSMember_Down = 101; 
    LHAPDFHandler_New.AlphaSMember_Up = 102; 
    LHAPDFHandler_New.init();

    pdfReweight->SetProdPDF( LHAPDFHandler_Prod.PDFCentral );
    pdfReweight->SetNewPDF( LHAPDFHandler_New.PDFCentral );
    pdfReweight->SetNewPDFErrorSet( LHAPDFHandler_New.PDFErrorSet );
    pdfReweight->SetNewPDFAlphaS( LHAPDFHandler_New.PDFAlphaSDown, LHAPDFHandler_New.PDFAlphaSUp );
  }
```

## 2. 오차 분석 단계 (executeEvent)

### 기본 객체 수집
-  모든 뮤온 수집
-  모든 제트 수집
-  [[L1Prefire]] 가중치 계산
 ```
 weight_Prefire = GetPrefireWeight(0);
 ```



### 시스템 분석 (RunSyst=true)
-   뮤온마다의 시스템 불확실성 계산
```c
for(unsigned int it_MuonID=0; it_MuonID<MuonIDs.size(); it_MuonID++){

    TString MuonID = MuonIDs.at(it_MuonID);
    TString MuonIDSFKey = MuonIDSFKeys.at(it_MuonID);

    param.Clear();

    param.syst_ = AnalyzerParameter::Central;

    param.Name = MuonID+"_"+"Central";

    param.Muon_Tight_ID = MuonID;
    param.Muon_ID_SF_Key = MuonIDSFKey;

    param.Jet_ID = "tight";

    executeEventFromParameter(param);


    if(RunSyst){

      for(int it_syst=1; it_syst<AnalyzerParameter::NSyst; it_syst++){
        param.syst_ = AnalyzerParameter::Syst(it_syst);
        param.Name = MuonID+"_"+"Syst_"+param.GetSystType();
        executeEventFromParameter(param);
      }

    }

    }
```

### PDF 분석 (RunNewPDF=true, !IsDATA)
-  PDF 재가중치 계산
-  오차 세트 분석
```c
 if(RunNewPDF && !IsDATA){
    //cout << "[ExampleRun::executeEvent] PDF reweight = " << GetPDFReweight() << endl;
    FillHist("NewPDF_PDFReweight", GetPDFReweight(), 1., 2000, 0.90, 1.10);
    //cout << "[ExampleRun::executeEvent] PDF reweight for error set (NErrorSet = "<<pdfReweight->NErrorSet<< ") :" << endl;
    for(int i=0; i<pdfReweight->NErrorSet; i++){
      //cout << "[ExampleRun::executeEvent]   " << GetPDFReweight(i) << endl;
      FillHist("NewPDF_PDFErrorSet/PDFReweight_Member_"+TString::Itoa(i,10), GetPDFReweight(i), 1., 2000, 0.90, 1.10);
    }
  }
```
### Cross Section 오차 추정 (RunXSecSyst=true)
-  PDF 오차 계산
-  AlphaS 변동 확인
-  Scale 변동 확인
```c
if(RunXSecSyst && !IsDATA){

    Event ev = GetEvent();
    double MET = ev.GetMETVector().Pt();

    for(unsigned int i=0; i<weight_PDF->size(); i++){
      FillHist("XSecError/MET_PDFError_"+TString::Itoa(i,10), MET, weight_PDF->at(i), 200, 0., 200.);
    }


    if(weight_AlphaS->size()==2){
      FillHist("XSecError/MET_PDFAlphaS_Down", MET, weight_AlphaS->at(0), 200, 0., 200.);
      FillHist("XSecError/MET_PDFAlphaS_Up", MET, weight_AlphaS->at(1), 200, 0., 200.);
    }

    for(unsigned int i=0; i<weight_Scale->size(); i++){
      if(i==5) continue;
      if(i==7) continue;
      FillHist("XSecError/MET_Scale_"+TString::Itoa(i,10), MET, weight_Scale->at(i), 200, 0., 200.);
    }

  }
```
## 3. 파라미터 설정 후 이벤트 설정 (executeEventFromParameter)

```c
void ExampleRun::executeEventFromParameter(AnalyzerParameter param)
// 각 param 안에 뮤온들과 뮤온의 파라미터값이 포함되어 있음

   param.Muon_Tight_ID = MuonID;
   param.Muon_ID_SF_Key = MuonIDSFKey;
   param.Jet_ID = "tight";
   param.syst_ = AnalyzerParameter::Central;
   param.Name = MuonID+"_"+"Central";
```

### 필수 통과 조건
-  MET 필터 통과
```c
    if(!PassMETFilter()) return;

    Event ev = GetEvent();
    Particle METv = ev.GetMETVector();
```
-  트리거 조건 만족
```c
    if(! (ev.PassTrigger(IsoMuTriggerName) )) return;
```
-  뮤온/제트 스케일링 적용
- ID 선택 기준 통과

### 가중치 적용 항목 (?)
-  MC 가중치
-  트리거 루미노시티
-  L1Prefire 재가중치
-  뮤온 스케일 팩터
``` c
if(param.syst_ == AnalyzerParameter::Central){

  }
  else if(param.syst_ == AnalyzerParameter::JetResUp){
    this_AllJets = SmearJets( this_AllJets, +1 );
    //this_AllFatJets = SmearFatJets( this_AllFatJets, +1 );
  }
  else if(param.syst_ == AnalyzerParameter::JetResDown){
    this_AllJets = SmearJets( this_AllJets, -1 );
    //this_AllFatJets = SmearFatJets( this_AllFatJets, -1 );
  }
  else if(param.syst_ == AnalyzerParameter::JetEnUp){
    this_AllJets = ScaleJets( this_AllJets, +1 );
    //this_AllFatJets = ScaleFatJets( this_AllFatJets, +1 );
  }
  else if(param.syst_ == AnalyzerParameter::JetEnDown){
    this_AllJets = ScaleJets( this_AllJets, -1 );
    //this_AllFatJets = ScaleFatJets( this_AllFatJets, -1 );
  }
  else if(param.syst_ == AnalyzerParameter::MuonEnUp){
    this_AllMuons = ScaleMuons( this_AllMuons, +1 );
  }
  else if(param.syst_ == AnalyzerParameter::MuonEnDown){
    this_AllMuons = ScaleMuons( this_AllMuons, -1 );
  }
  else if(param.syst_ == AnalyzerParameter::ElectronResUp){
    //this_AllElectrons = SmearElectrons( this_AllElectrons, +1 );
  }
  else if(param.syst_ == AnalyzerParameter::ElectronResDown){
    //this_AllElectrons = SmearElectrons( this_AllElectrons, -1 );
  }
  else if(param.syst_ == AnalyzerParameter::ElectronEnUp){
    //this_AllElectrons = ScaleElectrons( this_AllElectrons, +1 );
  }
  else if(param.syst_ == AnalyzerParameter::ElectronEnDown){
    //this_AllElectrons = ScaleElectrons( this_AllElectrons, -1 );
  }
  else{
    cout << "[ExampleRun::executeEventFromParameter] Wrong syst" << endl;
    exit(EXIT_FAILURE);
  }
```




### 이벤트 설정 & 주요 변수 체크리스트
 - pt, 각도 통과 기준 
 ```c
 vector<Muon> muons = SelectMuons(this_AllMuons, param.Muon_Tight_ID, 20., 2.4);
  vector<Jet> jets = SelectJets(this_AllJets, param.Jet_ID, 30., 2.4);
 ```

- Jet tagging
```c
    int NBJets_NoSF(0), NBJets_WithSF_2a(0);
  JetTagging::Parameters jtp_DeepCSV_Medium = JetTagging::Parameters(JetTagging::DeepCSV,
                                                                     JetTagging::Medium,
                                                                     JetTagging::incl, JetTagging::comb);
```

- B tagging
```c
    double btagWeight = mcCorr->GetBTaggingReweight_1a(jets, jtp_DeepCSV_Medium);

  //==== method 2a)
  for(unsigned int ij = 0 ; ij < jets.size(); ij++){

    double this_discr = jets.at(ij).GetTaggerResult(JetTagging::DeepCSV);
    //==== No SF
    if( this_discr > mcCorr->GetJetTaggingCutValue(JetTagging::DeepCSV, JetTagging::Medium) ) NBJets_NoSF++;
    //==== 2a
    if( mcCorr->IsBTagged_2a(jtp_DeepCSV_Medium, jets.at(ij)) ) NBJets_WithSF_2a++;

  }
```
- event selection
```c
     //==== dimuon
  if(muons.size() != 2) return;

  //==== leading muon has trigger-safe pt
  if( muons.at(0).Pt() <= TriggerSafePtCut ) return;

  //==== On-Z
  Particle ZCand = muons.at(0) + muons.at(1);
  if(!IsOnZ(ZCand.M(), 15.)) return;

```


-event weight 
```c
    double weight = 1.;
  //==== If MC
  if(!IsDATA){

    //==== MCweight is normalized to 1 pb-1.
    weight *= MCweight();

    //==== you can pass trigger names to ev.GetTriggerLumi(), but if you are using unprescaled trigger, simply pass "Full"
    weight *= ev.GetTriggerLumi("Full");

    //==== L1Prefire reweight
    weight *= weight_Prefire;

    //==== Example of applying Muon scale factors
    for(unsigned int i=0; i<muons.size(); i++){

      double this_idsf = 1.;
      //double this_idsf  = mcCorr->MuonID_SF (param.Muon_ID_SF_Key,  muons.at(i).Eta(), muons.at(i).MiniAODPt());

      //==== If you have iso SF, do below. Here we don't.
      //double this_isosf = mcCorr->MuonISO_SF(param.Muon_ISO_SF_Key, muons.at(i).Eta(), muons.at(i).MiniAODPt());
      double this_isosf = 1.;

      weight *= this_idsf*this_isosf;

    }

  }

```
### 시스템 플래그
-  RunSyst
-  RunNewPDF
-  RunXSecSyst

### 뮤온 설정
-  MuonIDs( 뮤온 선택 기준으로 사용)
    뮤온 식별을 위한 기본 ID 기준을 정의
    CMS POG(Physics Object Group)에서 정의한 표준 식별 기준
    실제 분석에서 뮤온을 선택할 때 사용
-  MuonIDSFKeys (MC 보정 인자 계산에 사용)
    Scale Factor(SF)를 적용하기 위한 키 값
    MC 시뮬레이션과 실제 데이터 간의 차이를 보정하는 데 사용
    NUM(Numerator): 적용할 ID 기준
    DEN(Denominator): 기준이 되는 뮤온 집합
-  IsoMuTriggerName
-  TriggerSafePtCut

### 분석 파라미터
-  syst_
-  Name
-  Muon_Tight_ID
-  Muon_ID_SF_Key
-  Jet_ID

### 출력 

```c
    FillHist(param.Name+"/ZCand_Mass_"+param.Name, ZCand.M(), weight, 40, 70., 110.);
```
## 참고사항
> - 시스템 분석은 RunSyst=true일 때만 실행
> - PDF 분석은 데이터가 아닌 경우만 실행
> - 연도별 트리거와 PT 컷 확인 필수

#CMS #ExampleRun #analysis #tutorial