> [!attention] Coffea를 배우기 전에 필요한 기본 도구에 익숙해야 합니다:
> 1. **NanoAOD 데이터 포맷**: Coffea는 CMS NanoAOD 형식의 데이터를 처리하는 데 최적화되어 있습니다. 기본적인 변수와 트리 구조를 이해해야 합니다
> 2.  **NumPy**와 **Awkward Array**: Coffea는 이 두 가지와 긴밀하게 통합되어 있습니다. **matplotlib**와 **mplhep**: 데이터 시각화에 필수적입니다.
> 3.  **병렬 처리**: Coffea는 Dask와 FuturesExecutor를 사용하여 데이터 병렬화를 지원합니다.
 
 - 예시 
 ```python 
from coffea import processor, hist
from coffea.nanoevents import NanoEventsFactory, NanoAODSchema
import matplotlib.pyplot as plt

# Processor 정의
class MuonProcessor(processor.ProcessorABC):
    def __init__(self):
	    # 히스토그램 정의
        self.hist = hist.Hist(
            "Counts",
            hist.Cat("dataset", "Dataset"), # 데이터셋 이름 카테고리
            hist.Bin("pt", "$p_T$ [GeV]", 50, 0, 200) # $p_T$ 히스토그램
        )
        self.accumulator = processor.dict_accumulator({"hist": self.hist})
    
    def process(self, events):
	    # 메타데이터에서 데이터셋 이름 가져오기
        dataset = events.metadata['dataset']
		# Muon 데이터 필터링: $p_T > 20$
        muons = events.Muon[events.Muon.pt > 20]
        self.accumulator["hist"].fill(dataset=dataset,pt=muons.pt.flatten())
        # 벡터를 평탄화하여 채움
        return self.accumulator
    
    def postprocess(self, accumulator):
        return accumulator

# 파일셋 정의 (각 데이터셋 이름과 ROOT 파일 목록)
fileset = {
    "signal": ["signal1.root", "signal2.root"],
    "background": ["background1.root", "background2.root"]
}

# Coffea Runner 실행
runner = processor.Runner(
    executor=processor.FuturesExecutor(workers=4),
    schema=NanoAODSchema,savemetrics=True)# NanoAOD 데이터 스키마 사용 , 실행 통계 저장
output = runner(fileset=fileset, treename="Events", processor_instance=MuonProcessor())

# 히스토그램 시각화
histogram = output['hist']
fig, ax = plt.subplots()
hist.plot1d(histogram, overlay="dataset", ax=ax)
plt.title("Muon $p_T$ Distribution")
plt.xlabel("$p_T$ [GeV]")
plt.ylabel("Counts")
plt.grid()
plt.show()
```









