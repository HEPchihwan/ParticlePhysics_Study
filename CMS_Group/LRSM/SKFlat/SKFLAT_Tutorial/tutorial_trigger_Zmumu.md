# Z → μμ Trigger 테스트 튜토리얼

> [!warning]
> tamsa 서버에서는 vs code 구동 x
>vs code로 cms 서버에서 작업 후 data6에 올려서 터미널에서 탐사서버로 올리기


## 목표
- [[Z → μμ 채널]]을 이용한 trigger 테스트 수행

## 코드 구조 및 설명

### 1. 초기화 단계
초기화 단계에서는 다음 설정들이 이루어집니다:
- 뮤온 ID 설정: [[POGMedium]], [[POGTight]] 사용
- Trigger 설정: 
  - 2017년 데이터용 [[HLT_IsoMu27_v]] 사용
  - TriggerSafePtCut = 29 GeV 설정
- 관련 코드: `initializeAnalyzer()` 함수 내 구현

```c
MuonIDs = {
		"POGMedium", 
		"POGTight"
	};
	if (DataYear == 2017) {
		IsoMuTriggerName = "HLT_IsoMu27_v";
		TriggerSafePtCut = 29.;
	}
	else {
		cerr << "[TutorialRun::InitializeAnalyzer] This tutorial is for year 2017" << endl;
		exit(EXIT_FAILURE);
	}
```

### 2. 이벤트 처리 단계
이벤트 처리는 다음 순서로 진행됩니다:

#### 기본 객체 수집
- 모든 뮤온과 제트를 수집

```c
AllMuons = GetAllMuons();
AllJets = GetAllJets();
```

- MET 필터 적용
- Trigger 조건 확인

```c
for (const auto& MuonID: MuonIDs) {
		FillHist(MuonID+"/Cutflow", 0., 1., 10, 0., 10.);
		if (!PassMETFilter()) return; 
		FillHist(MuonID+"/Cutflow", 1., 1., 10, 0., 10.); 
		Event ev = GetEvent();
		const Particle METv = ev.GetMETVector();

		if (! (ev.PassTrigger(IsoMuTriggerName))) return;
		FillHist(MuonID+"/Cutflow", 2., 1., 10, 0., 10.);		
```
#### 입자 선택 기준
- 뮤온 선택:
  - ID: POGMedium 또는 POGTight
  - pt > 20 GeV
  - |η| < 2.4
- 제트 선택:
  - tight ID
  - pt > 30 GeV
  - |η| < 2.4

```c
	vector<Muon> muons = SelectMuons(AllMuons, MuonID, 20., 2.4); 
		vector<Jet> jets = SelectJets(AllJets, "tight", 30., 2.4);
```


#### b-태깅 처리
- [[DeepJet]] 알고리즘 사용
- Medium working point 기준으로 [[b-제트]] 분류
- b-제트와 non-b-제트 분리 저장

```c
		vector<Jet> bjets, jets_nonb;
		for (const auto& jet: jets) {  
			const double this_discr = jet.GetTaggerResult(JetTagging::DeepJet); 
			if (this_discr > mcCorr->GetJetTaggingCutValue(JetTagging::DeepJet, JetTagging::Medium)) // 딥제트 결과가 중간 값보다 크면 bjet으로 추가
				bjets.emplace_back(jet);
			else
				jets_nonb.emplace_back(jet);
		}
```
#### Z 보손 재구성 조건
- 정확히 두 개의 뮤온 필요
```
if (muons.size() != 2) return; 
```
- 반대 전하를 가진 뮤온 쌍
```c
if (muons.at(0).Charge() + muons.at(1).Charge() != 0) return;
```
- 선두 뮤온 pt > TriggerSafePtCut

```c
if (! (muons.at(0).Pt() > TriggerSafePtCut)) return;
```

- Z 질량 조건: |m_μμ - 91.2 GeV| < 15 GeV

```c
Particle ZCand = muons.at(0) + muons.at(1);
const double mZ = 91.2; // GeV
if (! (fabs(mZ - ZCand.M()) < 15.)) return;
```

#### 가중치 적용
- MC 샘플의 경우:
  - gen_weight 적용
  - luminosity 보정
  - 최종 weight = gen_weight * luminosity

### 3. 분석 과제

#### Cutflow 구현
1. [[MET]] 필터 통과율
2. Trigger 효율
3. 뮤온 선택 효율
4. Z 질량 윈도우 통과율

#### 통계 분석
1. On-Z 영역 이벤트 수 계산
2. 통계적 오차 평가
   - Gaussian 분포 검증
   - √N 오차 확인

#### 운동학적 분석
1. 데이터-MC 비교
   - 선두 뮤온 분포
   - 차선두 뮤온 분포
2. 백그라운드 억제 연구
   - 추가 선택 기준 개발
   - 신호 민감도 최적화

## 참고 자료
- [CMS 뮤온 선택 가이드](https://twiki.cern.ch/twiki/bin/view/CMS/SWGuideMuonSelection)
- [CMS 뮤온 Trigger (2017)](https://twiki.cern.ch/twiki/bin/view/CMS/MuonHLT2017)
- [CMS b-태깅 권장사항](https://twiki.cern.ch/twiki/bin/view/CMS/BtagRecommendation)

#CMS #trigger #Zmumu #analysis #tutorial
