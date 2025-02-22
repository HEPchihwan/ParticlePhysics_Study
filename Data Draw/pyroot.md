uproot는 읽기만 가능한대신 빠름 . 

>[[주요 명령어들]]

- 파일 열기 
```python 
ROOT.TFile.Open(" file.root" )

# 반환 : Root.Tfile

file.ls() # 로 파일 내부 객체들 출력 가능 
```


- 트리 접근 
```python
tree = file.Get("Treename") # 트리 불러오기 
tree.GetListOfBranches() # 내부 브래치 확인 
tree.Draw("Branchname"). ; tree.GetBranch() ; # 브랜치 가져오기 
```


- 브랜치 내 내부 데이터 처리 
```python 
branch_data = [] 
if branch_data >0 #  
tree.GetEntry(i) # 이벤트 별 수동 접근 
```


- 히스토그램 생성
```python 
ROOT.TH1F( "name" , "title" , bins , x_min , x_max )
hist.Fill(value)
hist.Draw()
```


 - 예시
  트리 이름: TreeName

브랜치: Muon_pt, Electron_eta

 데이터:

| **Event Index** | **Muon_pt** | **Electron_eta** |
| --------------- | ----------- | ---------------- |
| 0               | [10.5,20.3] | [-1.2,0.5]       |
| 1               | [25.7]      | [0.3,-0.8]       |

```python

import ROOT

# ROOT 파일 열기

file = ROOT.TFile.Open("sample.root")

tree = file.Get("TreeName")  # 트리 이름에 맞게 변경

# 조건을 만족하는 이벤트의 Muon_pt 저장

filtered_muon_pts = []

  

for i, event in enumerate(tree):

    # 트리 안의 브랜치(event)중 Muonpt를 봄

    if event.Muon_pt > 20:

        tree.GetEntry(i)  # 이벤트 i를 가져옴

        filtered_muon_pts.append(tree.Muon_pt)

  

# 결과 확인

print(filtered_muon_pts)
```