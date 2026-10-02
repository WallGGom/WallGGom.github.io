# 하객 사진 업로드 서버 (Synology NAS)

하객이 `hjh-wedding.site/upload/` 에서 보낸 사진·영상을 NAS 공유폴더에 원본 그대로 저장한다.

```
하객 휴대폰 → photos.hjh-wedding.site (가비아 CNAME → xxx.synology.me)
  → 공유기 외부 443 → NAS 8443 (DSM 리버스 프록시) → tusd 컨테이너(127.0.0.1:18080) → /volume2/wedding-guests
```

- tusd: 끊기면 이어 올리는 업로드 서버. 10MB 단위로 받는다.
- 저장 이름: `20261011-143022_홍길동_a1b2c3_IMG_1234.HEIC` (한국 시각, 이름은 비었으면 생략)
- 받는 중인 파일은 `wedding-guests/.incoming` 에 쌓이고, 다 받으면 공유폴더 루트에 위 이름으로 나타난다.
(하드링크라 용량은 한 번만 든다. `.incoming` 쪽은 수집이 끝난 뒤 지워도 루트 파일은 남는다)
- 공유기 443 을 NAS 의 443 이 아니라 **8443** 으로 넘긴다. NAS 443 에는 DSM 기본 화면이 섞일 수 있어서, 이 규칙 하나만 있는 포트로 분리한다.
- 이 폴더는 GitHub Pages 로 공개된다. 비밀값은 넣지 않는다.

## 0. 준비

- `upload/` 페이지가 main 에 올라가 있어야 6단계 시험이 된다 (`https://hjh-wedding.site/upload/` 가 열리는지 확인).
- 패키지 센터 → **Container Manager** 설치. 설치하면 `docker` 공유폴더가 생긴다.
- NAS 내부 IP 를 메모한다 (DSM → 제어판 → 네트워크 → 네트워크 인터페이스). 예: `192.168.0.10`

## 1. 공유폴더 + 용량 상한 + 전용 계정

1. 제어판 → 공유 폴더 → 생성 → 이름 `wedding-guests`
2. 같은 화면 → 편집 → 고급 → **공유 폴더 할당량** 켜고 전체 상한 입력 (예: 200GB)
   - 항목이 안 보이면 볼륨이 ext4 다. 대신 4번에서 만든 `wedding-upload` 사용자 → 편집 → 할당량에서 같은 값을 건다.
3. 공유폴더가 `/volume1` 이 아닌 볼륨에 있으면 `docker-compose.yml` 의 `/volume1/wedding-guests` 를 고친다.
4. **업로드 전용 계정** — 컨테이너가 이 계정 권한으로 돈다. 뚫려도 `wedding-guests` 밖은 못 건드린다.
   - 제어판 → 사용자 및 그룹 → 생성 → 이름 `wedding-upload`, 긴 무작위 비밀번호 (로그인할 일 없음)
   - 그룹: `users` 만 (`administrators` 금지)
   - 공유 폴더 권한: `wedding-guests` 만 읽기/쓰기, 나머지 전부 액세스 없음
   - 애플리케이션 권한: 전부 거부
5. **계정 번호(UID) 확인** — DSM 화면엔 안 나와서 SSH 가 한 번 필요하다.
   - 제어판 → 터미널 및 SNMP → SSH 서비스 활성화
   - PC 에서 `ssh 관리자계정@NAS내부IP` (관리자 그룹 계정만 접속된다) → `id wedding-upload`
   - 예: `uid=1027(wedding-upload) gid=100(users)` → `1027` 과 `100` 을 메모
   - 확인했으면 SSH 를 다시 끈다.

## 2. 컨테이너 실행

1. `docker-compose.yml` 의 `user: "CHANGE_ME_UID:100"` 을 1-5 에서 본 값으로 바꾼다. 예: `user: "1027:100"`
   - gid 가 100 이 아니면 뒤 숫자도 바꾼다.
   - 안 바꾸면 `unable to find user CHANGE_ME_UID` 로 실행되지 않는다 (의도된 실패).
2. File Station → `docker` 공유폴더 아래 `wedding-upload` 폴더를 만들고, 이 `nas/` 폴더 내용을 올린다.
   - `Dockerfile`, `docker-compose.yml`, `hooks` 폴더(안에 `pre-create`, `post-finish`)
3. Container Manager → 프로젝트 → 생성
   - 프로젝트 이름: `wedding-upload` (소문자)
   - 경로: `/docker/wedding-upload` → 원본: 기존 `docker-compose.yml` 사용
   - 웹 포털(Web Station) 설정 화면이 나오면 설정하지 않고 다음 → 완료 (이미지를 빌드하고 실행한다)
4. 컨테이너 → `wedding-upload-tusd-1` → 로그에 `You can now upload files to:` 가 보이면 기동은 정상.
   - 쓰기 권한은 6단계에서 확인된다. 로그에 `permission denied` 가 뜨면 1-4 공유 폴더 권한을 다시 본다.

## 3. 외부 주소

1. **NAS 내부 IP 고정** — ipTIME 관리화면 → 고급 설정 → 네트워크 관리 → 내부 네트워크 설정(또는 DHCP 서버 설정) → 사용 중인 IP 목록에서 NAS 를 골라 수동 등록
2. **DDNS** — DSM 제어판 → 외부 액세스 → DDNS → 추가
   - 서비스 공급자 `Synology`, 호스트 이름 `원하는이름.synology.me`
   - 같은 화면의 "Let's Encrypt 인증서 받기"는 **체크하지 않는다** (4단계 80 차단 대안에서만 쓴다)
3. **가비아** — My가비아 → 도메인 → `hjh-wedding.site` → DNS 관리 → 레코드 추가
   - 타입 `CNAME`, 호스트 `photos`, 값 `원하는이름.synology.me.` (끝 점 포함)
   - 기존 GitHub Pages 레코드(A 4개)는 건드리지 않는다.
   - 몇 분\~몇 시간 뒤 PC 에서 `nslookup photos.hjh-wedding.site` → `synology.me` 이름과 집 공인 IP 가 나오면 반영된 것
4. **ipTIME 포트포워드** — 고급 설정 → NAT/라우터 관리 → 포트포워드 설정
   - 외부 443 → NAS 내부 IP **8443** (TCP)
   - 외부 80 → NAS 내부 IP 80 (TCP) — **인증서 발급할 때만** 켠다. 4-1 이 끝나면 이 규칙을 지운다.
    (NAS 80 에는 DSM 화면이 평문으로 섞일 수 있다. 인증서는 90일짜리라 수집 기간 동안 갱신이 필요 없다)
   - **5000·5001(DSM 관리 화면)은 열지 않는다.**
   - 고급 설정 → 보안 기능 → 공유기 접속 관리 에서 원격 관리 포트가 80·443 이면 다른 번호로 바꾸거나 끈다 (충돌).

## 4. 인증서 + 리버스 프록시

1. 제어판 → 보안 → 인증서 → 추가 → 새 인증서 추가 → Let's Encrypt 에서 인증서 얻기
   - 도메인 이름 `photos.hjh-wedding.site`, 이메일 입력
   - 3-3 의 `nslookup` 이 반영된 뒤에 한다.
   - **실패하면** 거의 80번이 바깥에서 안 열린 것이다. 포트포워드 누락을 먼저 보고, 맞는데도 실패하면 통신사가 80을 막은 것 → 맨 아래 "80 이 막혔을 때" 로 간다.
2. 제어판 → 로그인 포털 → 고급 → 리버스 프록시 → 생성
   - 소스: `HTTPS` / 호스트 이름 `photos.hjh-wedding.site` / 포트 **`8443`**, HSTS 켜기
   - 대상: `HTTP` / 호스트 이름 **`127.0.0.1`** / 포트 `18080` (`localhost` 는 IPv6 로 붙어 실패할 수 있다)
   - 사용자 지정 헤더 → 생성 → 두 줄 추가: `X-Forwarded-Proto` = `$scheme`, `X-Forwarded-Host` = `$host`
    (DSM 이 기본으로 넣는다는 보고가 있지만 확실치 않다. 중복이어도 무해하다)
3. 인증서 → 설정 → `photos.hjh-wedding.site` 항목에 1번 인증서 지정

## 5. 보안

1. 제어판 → 보안 → 계정
   - 자동 차단 켜기, 2단계 인증 켜기
   - 기본 `admin` 계정 비활성화 — 지금 `admin` 으로 쓰고 있다면 **다른 관리자 계정을 먼저 만들고** 그걸로 로그인한 뒤 끈다
2. 제어판 → 보안 → 방화벽 → 방화벽 활성화 → 프로필 편집 → 규칙. 규칙은 **위에서부터 처음 맞는 하나만** 적용된다. 이 순서로 넣는다:
   1. 포트 `모두` / 소스 IP `서브넷` NAS 내부 IP 대역 (예: `192.168.0.0` / `255.255.255.0`) / 허용 ← **맨 위. 빠뜨리면 집 안에서도 DSM 에 못 들어간다**
   2. (4-1 인증서를 이미 받았으면 넣지 않는다) 포트 `사용자 지정` TCP `80` / 소스 `모두` / 허용 — 나중에 재발급할 때만 잠깐. Let's Encrypt 는 해외 서버에서 확인하므로 국가 제한 금지
   3. 포트 `사용자 지정` TCP `8443` / 소스 `위치` `대한민국` / 허용 — 해외 하객도 받으려면 소스 `모두`
   4. 맨 아래 "규칙에 해당하지 않는 경우": **액세스 거부**
3. 할당량(1단계)이 전체 상한이다. 링크를 아는 사람은 누구나 올릴 수 있으니, 수집 기간이 끝나면 컨테이너를 중지하고 포트포워드를 지운다.

## 6. 확인 (휴대폰 Wi-Fi 끄고 LTE 로)

1. `https://photos.hjh-wedding.site/files/` → `method not allowed` 글자만 보이면 정상
   - DSM 로그인 화면 → 리버스 프록시 호스트 이름 불일치 (4-2)
   - 502 → 컨테이너가 안 돌거나 대상이 틀림 (2-4, 4-2)
   - 인증서 경고 → 4-3 지정 누락
2. `https://원하는이름.synology.me/`, `https://공인IP/`, `http://공인IP/` → DSM 로그인 화면이 뜨면 안 된다 (인증서 경고는 넘겨서 확인. 80 규칙을 지운 뒤면 `http://` 는 안 열리는 게 정상)
3. `https://hjh-wedding.site/upload/` 에서 사진 1장 + **10MB 넘는 동영상 1개** 보내기 → File Station `wedding-guests` 에 생기는지
   - 사진은 되는데 동영상만 "실패" → 리버스 프록시 업로드 크기 제한일 수 있다. 이 경우 tusd 로그엔 아무것도 안 남는다. 알려주면 조각 크기를 줄인다.
4. 아이폰으로 보낸 파일을 원본과 비교 — 사진 앱에서 고르면 HEIC→JPEG 변환이나 동영상 압축이 일어날 수 있다

## 80 이 막혔을 때 (인증서 발급 실패)

`photos.hjh-wedding.site` 대신 Synology 주소를 업로드 주소로 쓴다. 이 인증서는 80 없이 발급된다.

1. 3-2 DDNS 편집 → "Let's Encrypt 에서 인증서 받기" 체크 → `원하는이름.synology.me` 인증서 발급
2. 4-2 리버스 프록시 소스 호스트 이름을 `원하는이름.synology.me` 로, 4-3 에서 그 인증서 지정
3. `upload/index.html` 의 ENDPOINT 를 `https://원하는이름.synology.me/files/` 로 바꿔 푸시 (Claude 에게 요청)
4. 가비아 CNAME 과 80 포트포워드·방화벽 80 규칙은 지워도 된다

## 운영

- 업로드가 실패하면 Container Manager → 컨테이너 → 로그. `level=ERROR` 를 본다.
- 다 받았는데 루트에 안 나타난 파일은 `.incoming` 에만 있다 (로그에 `HookInvocationError`). `<id>.info` 에 원래 이름이 있다.
- 하객이 도중에 포기한 파일도 `.incoming` 에 남아 할당량을 차지한다. 수집이 끝난 뒤 `.incoming` 을 통째로 지운다.
- 누구나 올릴 수 있으니 수집 기간 밖에는 컨테이너를 중지한다. 기간 중엔 가끔 `wedding-guests` 용량을 본다 — 낯선 대량 파일이 보이면 컨테이너부터 멈춘다.
- Synology Photos 색인에는 수집이 끝나고 파일을 확인한 뒤 넣는다 (모르는 사람이 만든 파일을 NAS 가 썸네일 만들며 열지 않게).
- `nas/` 파일을 고치면 File Station 으로 덮어쓰고 Container Manager → 프로젝트 → 빌드.

