# SD(치비) 도슨트 캐릭터 — AI 생성 프롬프트 키트

이종호 ♥ 김희정 청첩장용. 목표: **같은 두 캐릭터**가 포즈만 바뀌며 여러 장.
프롬프트는 영어가 결과가 좋습니다(그대로 복붙). 한글은 설명용.

---

## 0. 사용법 (일관성이 전부)

1. **먼저 마스터 1장**을 마음에 들게 뽑는다 (아래 `wave` 포즈 추천).
2. 나머지 포즈는 그 마스터를 **이미지 참조**로 넣고 "같은 캐릭터·같은 스타일, 포즈만 바꿔줘"로 생성.
3. **닮게** 하려면: 실제 두 분 사진을 먼저 넣고 "이 커플을 아래 치비 스타일로" → 그 결과를 마스터로.

**툴별 팁**
- **나노바나나 / Gemini 이미지, ChatGPT 이미지**: 한 대화 안에서 마스터 이미지 + "keep these exact two characters and style, new pose: …" 로 이어가면 정체성 유지 잘 됨. (이 작업에 제일 무난)
- **Midjourney**: 마스터를 `--cref <url> --sref <url> --cw 100` 으로 참조.
- 가능하면 **같은 seed / 한 세션**에서 전부 뽑기.

출력: **투명 배경 PNG, 정사각 1024, 전신, 여백 넉넉히.**

---

## 1. 캐릭터 설정 (BIBLE — 모든 프롬프트에 그대로 포함)

```
Two chibi (super-deformed) characters — a Korean wedding couple, about 2.5 heads
tall — drawn in the SAME consistent style and identity in every image.
GROOM: young man, short neat black hair, warm friendly face, wearing a light
beige/tan suit, white shirt, soft gray tie, black dress shoes.
BRIDE: young woman, dark hair styled half-up, gentle smile, wearing a white
sleeveless tiered tulle wedding dress with small bow straps.
Both have simple cute faces (small dot eyes, tiny smile, soft pink cheeks).
Keep their faces, hair, and outfits IDENTICAL across all poses.
```

> 닮게 조정: 안경(thin round glasses), 앞머리, 헤어스타일 등 실제 모습에 맞춰 한 줄씩 고치세요.

## 2. 스타일 + 기술 스펙 (모든 프롬프트에 그대로 포함)

```
STYLE: soft chibi illustration, flat pastel colors with subtle cel shading,
clean thin line art, muted warm palette (cream, soft beige, gold/bronze #b08d57
accents) to match an elegant wedding invitation. Cute, refined, minimal —
not loud, not glossy 3D.
TECH: full body, centered, single clean pose, TRANSPARENT background (PNG alpha),
square 1:1, generous margin, soft even lighting, no background, no text.
```

## 3. 포즈별 프롬프트 (위 1+2 블록 뒤에 붙이기)

| 파일명 | 위치 | POSE (프롬프트에 추가) |
|--------|------|------------------------|
| `sd-wave.png` | 커버 (첫인사) | `POSE: standing close together, raising one hand to wave hello, warm welcoming smile.` |
| `sd-bow.png` | 모시는 글 | `POSE: side by side, both politely bowing ~50 degrees in a respectful Korean greeting (인사), hands at sides, gentle smiles.` |
| `sd-point.png` | 오시는 길 | `POSE: standing together, the bride pointing to the side with one hand as if showing the way, the groom smiling toward the same direction.` |
| `sd-guestbook.png` | 방명록 | `POSE: standing together, the bride holding a small pen and a tiny red heart, the groom holding a little open guest book, inviting expression.` |
| `sd-thanks.png` (선택) | 맺음말 | `POSE: bowing deeply together to say thank you, a few soft flower petals floating around them.` |

## 4. 네거티브(피할 것)

```
no background, no text, no watermark, no signature, no frame/border,
not photorealistic, no extra limbs, no extra fingers, do not change the
hairstyle or clothing between images, no dark or oversaturated colors.
```

## 5. 완성 후

- 각 파일 **투명 PNG·정사각**으로 저장 → 위 표의 파일명으로 `assets/` 에 넣기.
- "준비됐다" 하시면 스크롤 등장 + idle 흔들림 코드(라이브러리 0, reduced-motion 대응)를 짜서 끼웁니다.

## 6. 일관성 체크리스트

- [ ] 두 장 나란히 놓았을 때 **같은 사람**으로 보이나 (얼굴·헤어·옷)
- [ ] 배경 진짜 투명한가 (흰 배경 아님)
- [ ] 팔레트가 크림/금 톤인가 (쨍한 색 X)
- [ ] 전신 + 여백 (잘리지 않음)
- [ ] 작게 줄여도(≈100px) 알아볼 수 있나
