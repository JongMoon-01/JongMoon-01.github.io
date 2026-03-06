---
layout: post
title: "CCTV · 블랙박스 공조 실시간 차량 추적 시스템 시뮬레이션"
date: 2025-06-13 10:00:00 +0900
categories: [ITS, ComputerVision]
tags: [ITS, YOLO, OCR, LPR, SmartCity, Blender, React, Flask]
description: "YOLO 기반 차량 탐지와 OCR 번호판 인식을 활용한 ITS 시뮬레이션 프로젝트"
---

# 1. 캡스톤 디자인이란?
캡스톤(Capstone)은 돌기둥이나 담 위 등 건축물의 가장 높은 곳에 올려놓는 장식으로, 최고의 업적 혹은 성취를 뜻합니다. 캡스톤디자인(Capstone Design)은 전공 교육과정에서 배운 이론을 바탕으로, 과제(프로젝트) 수행을 통하여 문제 도출부터 해결까지의 과정을 경험하며 학업적 성취를 달성하고, 나아가 현장에 적용할 수 있는 현장 실무능력과 창의성을 갖춘 인력을 양성하기 위한 교육과정입니다.

대학 교육에 캡스톤디자인(Capstone Design)이라는 용어가 사용된 것은 2002년 「창의적 공학 교육 프로그램 개발 및 확산 사업」을 통하여 공학인증제도가 도입되고, 공학 인증을 위한 필수 교육과정으로 ‘설계(Design)’ 교과목이 지정되면서부터입니다. 캡스톤디자인(Capstone Design)은 3, 4학년을 대상으로 한 ‘종합설계’ 과정에 해당하며, 공학인증제도를 도입한 공과대학을 중심으로 발전되어 오다가, 2012년 시작된 산학협력선도대학육성사업(LINC사업)을 계기로 모든 학문 분야에 산학협력을 활성화하기 위하여 사업에 참여하는 모든 학과의 교과과정에 편성되어 지금의 캡스톤디자인(Capstone Design) 교육과정으로 자리 잡았습니다.

- 출처 : 서울과학기술대학교

복수전공 졸업 요건에 해당하는 필수 이수 과목이라 주전공 캡스톤 디자인도 진행하고 복전 캡스톤 디자인도 진행하게 되었다.

# 2. 주제 선정
 주제는 원래 캡디가 팀을 이뤄 프로젝트를 해야해서 타대학 학생들과 함께 하려고 첫 회의를 진행했는데, 일부러 자기들이 이미 만들어놓았던 프로젝트로 쉽게 가려고해서 팀에서 나오고 개인 프로젝트로 지능형교통체계(ITS : intelligent Transportation System)을 시뮬레이션 형태로 만들어보기로 했다. 

 원래 복수전공하면서 여러과의 커리큘럼을 살펴보았는데 그때 당시 한국에 C-V2X 인프라가 깔린다는 뉴스 기사를 보았었다. 그래서 V2X를 배우는 정보통신과로 복수전공을 하고 나중에 프로젝트를 진행하며 내가 배운것을 써먹어 보고 싶었었다. 개인 프로젝트인데 졸업요건인 캡스톤 디자인이라 상관 없겠다 싶어서 그냥 규모를 크게 잡았다.

# 3. 문제인식
고정형 CCTV만으로는 커버리지에 한계가 있다. 특히 지역별로 CCTV 밀집도가 들쭉날쭉해서 추적 사각지대가 생긴다. 그래서 “CCTV + 블랙박스”를 하나의 분산 센서처럼 묶어서 차량을 추적하는 구조를 시뮬레이션으로 구현했다.

파이프라인은 단순하다.

```
1. 영상 프레임에서 차량 탐지 (YOLOv12)

2. 탐지된 차량에서 번호판 인식 (Tesseract OCR)

3. 인식된 번호판 = 차량 ID로 시계열 로그를 연결해서 경로 추적

4. Blender로 만든 도시 + CCTV 배치 맵 위에 경로를 그려서 웹 대시보드로 시각화
```

결과적으로 번호판 입력 → 해당 차량이 지나간 CCTV 경로 표시가 핵심 기능이다.

# 4. 시스템 설계
이 프로젝트에서 제일 중요했던 건 모델 성능보다 데이터가 시스템 안에서 어떻게 흐르느냐 였다.

내가 만든 데이터 흐름은 아래처럼 단순하게 유지했다.

- Input: CCTV / 블랙박스 영상(시뮬레이션 영상 파일)
- Per-frame inference:
    - YOLO → 차량 bbox
    - bbox ROI → 전처리 → OCR → plate string

- Logging:
    - {timestamp, cctv_id, plate, bbox, conf} 형태로 이벤트 로그

- Query:
    - plate를 입력하면 해당 plate의 로그를 시간순으로 정렬 → (cctv_id sequence)

- Visualization:
    - CCTV 좌표(json) + cctv_id sequence를 polyline으로 draw

<center>
<img src="https://github.com/user-attachments/assets/044de268-ee34-4106-964d-0579d88cccf0" width="720" height=""/>
<p><b>[그림1]. Polyline draw하기 위한 차량 이동 경로 집합 </b></p>
</center>

즉, 실제 추적 알고리즘을 복잡하게 만들기보다 <b>plate 기반 event stitching</b>으로 추적을 단순화했다.
시뮬레이션에서는 이게 가장 안정적으로 동작할거라 생각했다.

# 5. Car Detection(YOLOv12)

모델/학습 설정
- Model : YoLOv12(M)
- 데이터셋 : 약 38500장, 7 Classes(차량 및 LCP)
- batch : 16
- lr : 0.01
- iou thershold : 0.5
- epochs : 50
성능(IOU >= 0.5)
- Precision : 97.1%
- Recall : 95.0%
- F1-score : 96.03%
- mAP@50-95 : 88.6%
  
우선 빠른 처리속도를 중요시 했고 모델 크기가 너무 크면 개인 컴퓨터로 학습돌리기도, 실행하기도 버거워서 M 크기로 진행했다. 그리고 ITS 특성상 이미지가 스트리밍처럼 프레임이 연속으로 들어오는 상황에서 안정적으로 빠르게 돌아가는게 중요하기 때문에 YOLO를 사용했다.

## 5.1. LCP Recognition (Tesseract OCR)

번호판 인식은 이전에는 EasyOCR로 진행을 했었고 이번에는 구글에서 제공하는 Tesseract OCR을 사용하기로 했다. 우선 블랙박스/CCTV 이미지는 도로 주행 특성상 기후 환경(비, 눈, 빛 반사), 환경 잡음에 영향을 많이 받는다. 그렇기 때문에 전처리를 통해서 이를 일관화 시키는 것이 중요해 다음과 같은 전처리 과정을 거쳤다. 

```
1. ROI 추출 : 차량 BBOX 내에서 번호판 후보 영역 CROP
2. Grayscale : 대비 확보
3. Adaptive Thresholding : 환경 조도 변화 대응
4. Morphology : 작은 노이즈 제거 / 글자 굵기 안정화
5. Perspective Transform + Resize : 기울어진 번호판 보정 / OCR 적정 해상도(예 : 128x64)로 정규화
```

## 5.2. Tessearct OCR 설정
- PSM(Page Segmentation Mode) : 7
- OEM(OCR Engine Mode) : 3
- LANG : kr+eng

# 6. 시뮬레이션 상에서 Tracking을 딥러닝하지 않은 이유
원래는 ReID나 Traker(ex. DeepSORT)를 붙여서 객체 추적을 진행하는 방식도 고려를 했다. 하지만 이번 프로젝트에선는 <b>서로 다른 CCTV/블랙박스에서 동일 차량을 찾아 도시 단위에서 차량의 이동 경로를 묶는 걸 목표</b>로 했기 때문에 추적을 아래와 같이 정의했다.

- plate가 동일하면 동일 차량
- {time, cctv_id} 로그를 시간순 정렬하면 경로가 된다.
이 방식은 단점도 있다.
- OCR이 틀리면 다른 차량으로 분기됨
- 번호판이 가려지면 공백이 생김

하지만 시뮬레이션 환경에서 여러 잡음은 배제할 수 있게 설정이 가능해 설명이 가능한 이론 검증용 프로그램으로는 괜찮겠다 싶었다.

# 7. 도시/차량/CCTV 시뮬레이션 (Blender 4.2)
실험을 위해 도시를 구성했다. 도시는 UE5 에서 제공하는 ASSET을 활용하여 아파트 단지, 공업 단지, 공원, 2차선 도로가 존재하는 도시를 구현했다.

렌더링은 Blender로 진행했기 때문에 렌더링 시 부하를 막기 위해 Parallax Mapping을 적용해 중복 오브젝트 최적화를 진행해주었다.

<center>
<img src="https://github.com/user-attachments/assets/f339fb58-358e-49dd-a61f-156e59346c45" width="720" height=""/>
<p><b>[그림2]. 도시 TopView </b></p>
</center>

## 7.1 차량 모델링
2020년에 Blender 처음 배울때 BlenderBop이라는 유튜브 채널에서 BP를 이용해서 복잡한 모델링을 복사기처럼 따라 만드는 모델링 방법이 존재했는데 이를 사용해서 모델링을 진행했다.

<center>
<img src="https://github.com/user-attachments/assets/73d710cf-af85-4e1f-9c3f-5072effd8879" width="720" height=""/>
<p><b>[그림3]. BluePrint 모델링 방법 </b></p>
</center>

또한 차량의 움직임도 어느정도 구현하고 싶어서 Blender에서 지원하는 Add-on중 하나인 RigCar를 사용해 차량의 Bumping, Wheel motion, Braking 등을 구현했다.

<center>
<img src="https://github.com/user-attachments/assets/e85450ff-14aa-4184-92f2-4ff82c3223f3" width="720" height=""/>
<p><b>[그림4]. RigCar 활용 </b></p>
</center>

# 8. Web Dashboard(React + flask)
원래 계획은 Mpeg Streaming 서버까지 만들어 CCTV에서 들어오는 영상을 MPD로 저장해 추적 알고리즘에 적용하려 했었는데, 프로젝트 기간이 한달 정도였어서 마지막 부분에서 힘이 좀 많이 빠졌다.

구현한 기능은 다음과 같다.
- 지도 위 CCTV 점 클릭 → /api/cctv/<id> 호출 → 영상 URL 받아서 player에 띄움

- 번호판 입력 → (시뮬레이션에서는 setTimeout으로) 검색 완료 → 경로 polyline 표시

- 로그 창에 이벤트 출력

## 8.1 Flask API

{% raw %}
```python
from flask import Flask, jsonify
from flask_cors import CORS

app = Flask(__name__, static_folder='static')
CORS(app)

CCTV_VIDEO_MAP = {
  1: "CCTV7.mkv",
  2: "CCTV6.mkv",
  3: "CCTV5.mkv",
  4: "CCTV4.mkv",
  5: "CCTV3.mkv",
  6: "CCTV2.mkv",
  7: "CCTV1.mkv",
}

@app.route('/api/cctv/<int:cctv_id>')
def get_cctv_video(cctv_id):
    filename = CCTV_VIDEO_MAP.get(cctv_id)
    if not filename:
        return jsonify({"error": "Video not found"}), 404
    return jsonify({"video_url": f"http://localhost:5000/static/{filename}"})

if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

## 8.2. React

```html
import React, { useState, useRef } from 'react'; 
import mapImage from './map.png'; 
import cctvPositions from './cctv_positions.json'; 
  
export default function Dashboard() { 
  const [selectedCCTVs, setSelectedCCTVs] = useState([]); 
  const [targets, setTargets] = useState([]); 
  const [logs, setLogs] = useState([]); 
  const [searchPlate, setSearchPlate] = useState(''); 
  const [showPaths, setShowPaths] = useState(false); 
  const mapRef = useRef(); 
  
  // ✅ 경로 정의 (CCTV id 순서대로) 
  const path1Ids = [1,2,3,4,5,6,7]; 
  const path2Ids = [1, 4]; 
  
  const getPathCoords = (ids) => 
    ids.map(id => { 
      const found = cctvPositions.find(c => c.id === id); 
      return found ? { x: found.x, y: found.y } : null; 
    }).filter(Boolean); 
  
  const path1 = getPathCoords(path1Ids); 
  const path2 = getPathCoords(path2Ids); 
  
  // ✅ % → px 변환 
  const convertPercentToPx = (xPercent, yPercent) => { 
    const box = mapRef.current?.getBoundingClientRect(); 
    if (!box) return { x: 0, y: 0 }; 
    return { 
      x: (xPercent / 100) * box.width, 
      y: (yPercent / 100) * box.height 
    }; 
  }; 
  
17 | Page 
 
  // ✅ CCTV 클릭 시 영상 요청 
  const handleMapClick = async (cctvId) => { 
    if (selectedCCTVs.some(c => c.id === cctvId)) { 
      setLogs(prev => [...prev, `CCTV ${cctvId} already 
selected.`]); 
      return; 
    } 
    try { 
      const res = await 
fetch(`http://localhost:5000/api/cctv/${cctvId}`); 
      const data = await res.json(); 
      const label = cctvPositions.find(c => c.id === cctvId)?.label 
|| `CCTV ${cctvId}`; 
      if (data.video_url) { 
        setSelectedCCTVs(prev => [...prev, { id: cctvId, label, src: 
data.video_url }]); 
        setLogs(prev => [...prev, `${label} clicked.`]); 
      } else { 
        setLogs(prev => [...prev, `Video for CCTV ${cctvId} not 
found.`]); 
      } 
    } catch { 
      setLogs(prev => [...prev, `Error fetching video for CCTV 
${cctvId}`]); 
    } 
  }; 
  
  // ✅ 차량 검색 
  const handleSearch = () => { 
  setLogs(prev => [...prev, `차량 번호 ${searchPlate} 검색 중...`]); 
  setShowPaths(false); // 경로 먼저 숨기기 
  setTargets([]);      // 타겟 이미지도 초기화 
  
  setTimeout(() => { 
    setTargets(["/Car-Front.png", "/Car.png"]); 
    setShowPaths(true); 
    setLogs(prev => [...prev, `검색 완료: 차량 번호 ${searchPlate}`]); 
  }, 2000); // 2초 후 실행 
}; 
  
  return ( 
    <div className="grid grid-cols-3 h-screen gap-2 p-2 bg-gray
100"> 
      {/* Map + Search */} 
      <div className="col-span-1 flex flex-col gap-2"> 
        <div className="bg-white rounded shadow p-2 flex-1"> 
          <h2 className="font-bold text-lg mb-2">MAP</h2> 
18 | Page 
 
          <div className="relative w-full aspect-square" 
ref={mapRef}> 
            <img src={mapImage} alt="map" className="w-full h-full 
object-cover rounded" /> 
  
            {/* 🔵 SVG 경로 표시 */} 
            <svg className="absolute top-0 left-0 w-full h-full 
pointer-events-none"> 
              {showPaths && ( 
                <> 
                  <polyline 
                    points={path1.map(p => { 
                      const { x, y } = convertPercentToPx(p.x, p.y); 
                      return `${x},${y}`; 
                    }).join(" ")} 
                    stroke="blue" 
                    strokeWidth="3" 
                    fill="none" 
                  /> 
                  {[...path1, ...path2].map((p, i) => { 
                    const { x, y } = convertPercentToPx(p.x, p.y); 
                    return <circle key={i} cx={x} cy={y} r="5" 
fill="red" />; 
                  })} 
                </> 
              )} 
            </svg> 
  
            {/* 🔴 CCTV 점 */} 
            {cctvPositions.map(cctv => ( 
              <div 
                key={cctv.id} 
                onClick={() => handleMapClick(cctv.id)} 
                className="absolute bg-red-500 w-3 h-3 rounded-full 
cursor-pointer" 
                style={{ 
                  top: `${cctv.y}%`, 
                  left: `${cctv.x}%`, 
                  transform: 'translate(-50%, -50%)' 
                }} 
                title={cctv.label} 
              /> 
            ))} 
          </div> 
          <button 
            onClick={() => { 
              setSelectedCCTVs([]); 
              setShowPaths(false); 
19 | Page 
 
            }} 
            className="mt-2 px-3 py-1 bg-blue-600 text-white 
rounded" 
          >Clear</button> 
        </div> 
  
        <div className="bg-white rounded shadow p-2"> 
          <input 
            type="text" 
            placeholder="차량 번호 입력" 
            value={searchPlate} 
            onChange={(e) => setSearchPlate(e.target.value)} 
            className="border p-1 w-full mb-2" 
          /> 
          <button onClick={handleSearch} className="w-full bg-blue
700 text-white px-3 py-1 rounded"> 
            차량 검색 
          </button> 
        </div> 
      </div> 
  
      {/* Target Preview */} 
      <div className="col-span-1 bg-white rounded shadow p-2"> 
        <h2 className="font-bold mb-2">Target Container</h2> 
        <div className="grid grid-cols-1 gap-2"> 
          {targets.map((src, idx) => ( 
            <div key={idx} className="aspect-video w-full border 
rounded overflow-hidden"> 
              <img src={src} alt={`target-${idx}`} className="w-full 
h-full object-cover" /> 
            </div> 
          ))} 
        </div> 
      </div> 
  
      {/* CCTV Viewers */} 
      <div className="col-span-1 bg-white rounded shadow p-2 
overflow-y-auto max-h-[calc(100vh-8rem)]"> 
        <h2 className="font-bold mb-2">Clicked CCTV Viewers</h2> 
        <div className="grid grid-cols-1 gap-2"> 
          {selectedCCTVs.map((cctv, idx) => ( 
            <div key={idx} className="aspect-video w-full border 
rounded overflow-hidden"> 
              <div className="bg-gray-800 text-white text-sm px-2 py
1">{cctv.label}</div> 
              <video controls className="w-full h-full object-cover"> 
                <source src={cctv.src} type="video/mp4" /> 
              </video> 
</div> 
))} 
</div> 
</div> 
{/* Log Box */} 
<div className="col-span-3 bg-black text-white p-2 mt-2 
rounded font-mono text-sm overflow-y-auto h-32"> 
{logs.map((log, idx) => ( 
<div key={idx}>{log}</div> 
))} 
</div> 
</div> 
); 
} 
```
{% endraw %}

# 9. 프로젝트를 진행하면서 느낀 점
해당 프로젝트를 진행하고 그 다음해에 Aivle에서 Tableau나 istio 같은 배포툴을 알게 되어서 이 프로젝트를 만들때 해당 기술들을 적용했으면 백엔드 구축이 더 빨라지고 대쉬보드의 퀼리티가 많이 향샹 됐을거 같다는 생각이 들었다.

이를 통해 매해 국토교통부에서 CV2X 공모전 주최하는데 해당 프로젝트를 잘 다듬어 올해 6월쯤 공모전에 제출해보려고 생각하고 있다.

그리고 모델 하나 잘 만드는 것보다 더 빡센 게 시스템 통합이었다.

- 프레임 단위 인식 결과를 어떤 형태로 저장할지

- OCR 실패를 어떻게 필터링할지

- CCTV 좌표/이벤트 로그/쿼리 결과를 어떻게 UI에 매핑할지

이런 설계가 없으면, 모델 성능이 좋아도 시스템이 안 굴러가고 사용자 입장에서 사용하기 너무 까다롭다. 까다롭다는 표현도 살짝 명확하지 않긴한데, 사용감이 떨어지는 서비스는 손이 안가고 익숙해지는데도 오래 걸리는 그 느낌이다.
