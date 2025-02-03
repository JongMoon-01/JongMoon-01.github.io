---
title: "타로 카드 셔플 홈페이지 구현"
date: 2024-06-18 02:23:00 +0900
categories: [Tarot]
tags: [Occult, Astrology, ]
---
# 1. 소개
 타로 카드 덱이 없어서 코딩으로 금방 구현하지 않을까 싶어서 간단한 html 파일로 셔플과 카드 선택, 선택한 카드 상세 설명 살펴보기 기능을 구현해서 만들어 봤다.

# 2. 코드
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>타로 카드 셔플</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            height: 100vh;
            margin: 0;
            background-color: #f4f4f4;
        }

        #tarot-container, #selected-cards {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            justify-content: center;
        }

        #tarot-container {
            width: 80%;
        }

        .card {
            width: 100px;
            height: 150px;
            cursor: pointer;
            perspective: 1000px;
            transition: transform 0.5s, width 0.5s, height 0.5s;
        }

        .card img {
            width: 100%;
            height: 100%;
            transition: transform 0.5s;
            transform-style: preserve-3d;
        }

        .card.flipped img {
            transform: rotateY(180deg);
        }

        .hidden {
            display: none;
        }

        .large {
            width: 225px; /* 선택한 카드의 너비 */
            height: 375px; /* 선택한 카드의 높이 */
        }

        .expanded {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 300px;
            height: 450px;
            z-index: 10;
        }

        .description-container {
            position: fixed;
            top: 50%;
            left: 60%;
            transform: translateY(-50%);
            background: white;
            padding: 20px;
            border: 1px solid #ccc;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            z-index: 10;
        }

        .description-container .close-button {
            position: absolute;
            top: 10px;
            right: 10px;
            cursor: pointer;
        }

        @keyframes shuffle {
            0% { transform: translateY(0); }
            25% { transform: translateY(-20px); }
            50% { transform: translateY(20px); }
            100% { transform: translateY(0); }
        }

        .shuffle-animation {
            animation: shuffle 0.5s infinite;
        }

        button {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div id="tarot-container"></div>
    <div id="selected-cards" style="gap: 150px;"></div>
    <button onclick="shuffleCards()">셔플</button>

    <script>
        const tarotCards = [
            { id: 22, img: 'card22.jpg', description: '새로운 시작과 무한한 가능성을 상징하며, 모험과 순수함을 나타냅니다.' },
            { id: 1, img: 'card1.jpg', description: '창의성과 의지의 발현을 나타내며, 목표 달성을 위한 자원을 가리킵니다.' },
            { id: 2, img: 'card2.jpg', description: '직관과 내면의 지혜를 상징하며, 숨겨진 진실과 비밀을 나타냅니다.' },
            { id: 3, img: 'card3.jpg', description: '풍요와 창조성을 나타내며, 모성애와 자연의 힘을 상징합니다.' },
            { id: 4, img: 'card4.jpg', description: '권위와 구조를 상징하며, 질서와 통제를 나타냅니다.' },
            { id: 5, img: 'card5.jpg', description: '전통과 영적 가르침을 나타내며, 종교적 믿음과 사회적 규범을 상징합니다.' },
            { id: 6, img: 'card6.jpg', description: '사랑과 조화를 상징하며, 중요한 선택과 관계의 깊이를 나타냅니다.' },
            { id: 7, img: 'card7.jpg', description: '의지와 승리를 나타내며, 역경을 극복하는 능력을 상징합니다.' },
            { id: 8, img: 'card8.jpg', description: '내적 힘과 용기를 상징하며, 부드러움과 인내의 중요성을 나타냅니다.' },
            { id: 9, img: 'card9.jpg', description: '고독과 내적 탐구를 나타내며, 지혜와 명상을 상징합니다.' },
            { id: 10, img: 'card10.jpg', description: '운명의 변화를 상징하며, 인생의 예측 불가능성을 나타냅니다.' },
            { id: 11, img: 'card11.jpg', description: '균형과 공정을 나타내며, 책임과 결과의 중요성을 상징합니다.' },
            { id: 12, img: 'card12.jpg', description: '새로운 관점과 희생을 상징하며, 상황을 다르게 보는 능력을 나타냅니다.' },
            { id: 13, img: 'card13.jpg', description: '변화와 변혁을 나타내며, 끝과 새로운 시작의 상징입니다.' },
            { id: 14, img: 'card14.jpg', description: '조화와 절제를 상징하며, 균형과 중용의 중요성을 나타냅니다.' },
            { id: 15, img: 'card15.jpg', description: '유혹과 속박을 나타내며, 물질적 집착과 중독을 상징합니다.' },
            { id: 16, img: 'card16.jpg', description: '갑작스러운 변화와 혼란을 상징하며, 예기치 않은 파괴와 재건을 나타냅니다.' },
            { id: 17, img: 'card17.jpg', description: '희망과 영감을 나타내며, 치유와 평화의 상징입니다.' },
            { id: 18, img: 'card18.jpg', description: '환상과 무의식을 상징하며, 불확실성과 감정의 깊이를 나타냅니다.' },
            { id: 19, img: 'card19.jpg', description: '행복과 성공을 나타내며, 긍정적인 에너지와 성취의 상징입니다.' },
            { id: 20, img: 'card20.jpg', description: '부활과 자기 평가를 상징하며, 새로운 시작과 도약을 나타냅니다.' },
            { id: 21, img: 'card21.jpg', description: '완성과 성취를 나타내며, 인생의 모든 주기의 완성을 상징합니다.' }
        ];
        let selectedCards = [];

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        function shuffleCards() {
            const container = document.getElementById('tarot-container');
            const selectedContainer = document.getElementById('selected-cards');
            container.innerHTML = '';
            selectedContainer.innerHTML = '';
            selectedCards = [];

            const shuffledCards = shuffle([...tarotCards]);
            shuffledCards.forEach((card) => {
                const cardElement = document.createElement('div');
                cardElement.className = 'card';
                cardElement.dataset.id = card.id;
                cardElement.dataset.img = card.img;
                cardElement.dataset.description = card.description;
                cardElement.onclick = () => selectCard(cardElement);

                const imgElement = document.createElement('img');
                imgElement.src = 'card_back.jpg';
                imgElement.alt = `타로 카드 ${card.id}`;

                cardElement.appendChild(imgElement);
                container.appendChild(cardElement);
            });

            const cardElements = document.querySelectorAll('.card');
            cardElements.forEach(card => card.classList.add('shuffle-animation'));

            setTimeout(() => {
                cardElements.forEach(card => card.classList.remove('shuffle-animation'));
            }, 3000);
        }

        function selectCard(cardElement) {
            if (selectedCards.length < 3 && !selectedCards.includes(cardElement)) {
                selectedCards.push(cardElement);

                const imgSrc = cardElement.dataset.img;
                cardElement.querySelector('img').src = imgSrc;
                

                const selectedContainer = document.getElementById('selected-cards');
                const selectedCardElement = cardElement.cloneNode(true);
                selectedCardElement.classList.add('large');
                selectedCardElement.onclick = () => expandCard(selectedCardElement);
                selectedContainer.appendChild(selectedCardElement);

                cardElement.style.pointerEvents = 'none';
            }
        }

        function expandCard(cardElement) {
            cardElement.classList.add('expanded');

            const descriptionContainer = document.createElement('div');
            descriptionContainer.className = 'description-container';

            const descriptionText = document.createElement('p');
            descriptionText.innerText = cardElement.dataset.description;
            descriptionContainer.appendChild(descriptionText);

            const closeButton = document.createElement('span');
            closeButton.innerText = 'X';
            closeButton.className = 'close-button';
            closeButton.onclick = () => {
                cardElement.classList.remove('expanded');
                document.body.removeChild(descriptionContainer);
            };
            descriptionContainer.appendChild(closeButton);

            document.body.appendChild(descriptionContainer);
        }

        // 초기 셔플
        shuffleCards();
    </script>
</body>
</html>

```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/9dbff8df-56b5-4db9-b854-7e3c2c049461" width="840" height=""/>
<p><b>[그림1]. 켈트 십자가 </b></p>
</center>

