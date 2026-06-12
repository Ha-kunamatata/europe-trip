# 유럽 여행 준비 체크리스트 (PWA)

파리 · 인터라켄 · 취리히 · 로마 신혼여행용 체크리스트.
홈 화면에 추가하면 앱처럼 실행되고, 체크 상태는 기기에 자동 저장됩니다(오프라인 동작).

## 파일 구성
```
europe-trip/
├─ index.html              # 앱 본체 (localStorage 저장)
├─ manifest.webmanifest    # 앱 이름·아이콘·테마
├─ sw.js                   # 오프라인 캐시 서비스워커
└─ icons/                  # 앱 아이콘 (192/512/maskable/apple)
```

---

## 방법 A. GitHub 웹에서 올리기 (가장 간단, CLI 불필요)

**기존 `ha-kunamatata.github.io` 저장소의 하위 폴더로 추가하는 경우**
1. https://github.com/ha-kunamatata/ha-kunamatata.github.io 접속
2. `Add file → Upload files`
3. `europe-trip` 폴더 안의 파일들을 `europe-trip/` 경로가 유지되도록 드래그 (폴더째 올리기)
4. `Commit changes`
5. 1~2분 뒤 접속: **https://ha-kunamatata.github.io/europe-trip/**

**새 저장소로 만드는 경우**
1. 새 repo 생성 (예: `europe-trip`, Public)
2. 파일 업로드 → Commit
3. `Settings → Pages → Branch: main / root` 저장
4. 접속: **https://ha-kunamatata.github.io/europe-trip/**

> ⚠️ `.github.io`는 HTTPS라 PWA·서비스워커가 정상 동작합니다.

---

## 방법 B. Git CLI / Claude Code로 올리기

```bash
# 1) 폴더로 이동
cd europe-trip

# 2) (새 repo일 때) 초기화
git init
git add .
git commit -m "유럽 여행 체크리스트 PWA"

# 3) 원격 연결 후 푸시  (USERNAME/REPO 본인 것으로)
git remote add origin https://github.com/ha-kunamatata/europe-trip.git
git branch -M main
git push -u origin main

# 4) GitHub → Settings → Pages → Branch main/root 활성화
```

Claude Code에서 이어서 작업하려면 아래 프롬프트를 붙여넣으세요:

```
이 폴더는 GitHub Pages에 올릴 PWA 체크리스트 앱이야.
구조: index.html(앱 본체, localStorage 저장), manifest.webmanifest, sw.js, icons/.
체크 항목 데이터는 index.html 안의 <script> 상단 const DATA 배열에 들어있어
(각 항목: {id, name, type:"buy|have", ess:true/false, note}).
다음 작업을 도와줘:
1) DATA에 항목 추가/수정/카테고리 신설
2) 로컬 미리보기: python3 -m http.server 8080 로 띄우고 확인
3) 변경 후 git add/commit/push 까지
폰에서 PWA로 설치해 쓸 거라, 경로는 전부 상대경로 유지하고
서비스워커 캐시 버전(sw.js의 CACHE = "eutrip-v1")은 내용 바뀌면 올려줘.
```

---

## 폰에서 앱처럼 설치하기

- **아이폰(Safari)**: 페이지 열기 → 공유 버튼 → `홈 화면에 추가`
- **안드로이드(Chrome)**: 페이지 열기 → 우상단 ⋮ → `앱 설치` 또는 `홈 화면에 추가`

설치하면 주소창 없이 전체화면 앱으로 뜨고, 비행기/지하철에서도 오프라인으로 열립니다.

---

## 항목 직접 편집하기 (index.html)

`<script>` 안의 `const DATA = [...]` 에서 추가·삭제·수정.
```js
{ cat:"새 카테고리", items:[
  {id:"z1", name:"항목 이름", type:"buy",  ess:true,  note:"메모"},  // 🛒 살 것
  {id:"z2", name:"다른 항목", type:"have", ess:false, note:""},      // 🎒 챙길 것
]},
```
- `id`는 다른 항목과 겹치지 않게(체크 저장 키로 쓰임).
- 앱 안의 **‘내 항목 추가’** 로 넣은 항목은 기기에만 저장됩니다. 영구로 넣으려면 DATA에 직접 추가하세요.
- `sw.js` 내용까지 바꿨다면 `CACHE` 버전(`eutrip-v1`→`eutrip-v2`)을 올려야 폰에서 갱신됩니다.

## 백업 / 복원
앱 하단의 **백업 내보내기** 로 JSON 저장, **복원** 으로 불러오기.
(기기·브라우저를 바꾸거나 데이터 삭제 시 대비용)
