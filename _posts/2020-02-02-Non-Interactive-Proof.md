---
title: "Non-Interactive ZKP"
date: 2020-01-06 17:26:28 -0400
background: "/img/classic_blue.jpeg"
categories: 
    - cryptography 
    - non_interactie_zkp 
    - ZeroKnowledgeProof
---

## ZKP의 한계
zkp의 경우 증명자가 항상 on-line 상태여야하는 한계가 있습니다..  
따라서 non-interactive하게 즉, 증며자 혹은 인증자의 on-line 관계없이 증명할 수 있도록 하기 위해서는 결국 증명자와 인증자 사이의 메시지 교환이 최소화되어야 한다.  
<br/>
<br/>
증명자가 어떠한 사실을 증명하기 위해서 한번의 메시지를 보낸 후 연결이 끊어진다고 하더라도 증명하고자 한 사실이 증명이 되어야하는 것이 non-interactive proof의 핵심입니다.
<br/>
<br/>
암호학에서 Schnorr Protocol 은 증명자가 자신의 개인키를 공개하지 않고도 이를 가지고 있다는 것을 non-interactive한 방식으로 증명할 수 있는 방법입니다.
<br/>
<br/>
먼저, 증명자만 알고 있는 변수과 증명자와 인증자가 같이 공유하는 값들은 다음과 같습니다.
![Untitled](https://user-images.githubusercontent.com/57262833/73606130-47f31c00-45ea-11ea-94e6-750cc2c15a1d.png)
증명자는 랜던함 값인 r을 하나 선택하고 이 r값을 이용하여 X를 계산합니다.  
<br/>
<br/>
그 후 증명자는 인증자에게 보내고자 하는 메시지 M과 X값을 이어 이어 붙인 후,  
이 M과 X의 이어 붙인 것에 대한 해시함수값을 구합니다. 이 해시함수 값이 e에 해당하며 서명값으로서 쓰입니다.  
<br/>
<br/>
그리고 증명자는 이 e값을 이용하여 또다른 서명값에 해당하는 y값을 도출해 냅니다.  
증명자는 메시지 M과 만들어낸 서명 e,y를 인증자에게 보냅니다.  
<br/>
<br/>
이 방식은 non-interactive하기 때문에 증명자는 메시지를 보낸 후 이 자리에서 사라져 더이상의 응답을 할 수 없더라도 증명에는 지장이 없습니다.  
![Untitled](https://www.notion.so/2-33d06921f1904e63bd47c80e2b4c711d#0779ad99586a468a9063da7d08ff08a0)
<br/>
<br/>
인증자가 증명자로부터 받은 정보를 통해 수행해야할 것은 받은 M, e, y를 통해 만든 다시 만들어낸 X(X')과 증명자가 가지고 있는 X와 일치하는지를 확인하는 것 입니다.  
따라서 다음 과정을 거쳐서 X'을 만들어낼 수 있습니다.  
<br/>
<br/>
![Untitled](https://www.notion.so/2-33d06921f1904e63bd47c80e2b4c711d#68158be3dd2647d3b23d06ceb90c0b8d)
<br/>
<br/>
이 과정을 통해서 X'과 X가 같은지 알 수 있습니다.  
하지만 여기서 증명을 끝낸다면 검증자가 X'을 도출했어도, 실제 X와 같은지는 아직까지 의문이다.  
<br/>
<br/>
그런데 여기서 X는 증명자만 알고 있는 정보이며 검증자가 증명자로 부터 전달받지 못했기 때문에,  
인증자가 만든 X와 같은지를 직접적으로 비교하여 알 수 없습니다.(직접적으로 비교한 것이 아니기 때문이다.)  
<br/>
<br/>
따라서 인증자는 도출한 X'을 이용하여 e값을 도출해냅니다.
<br/>
<br/>
도출해낸 e값을 e프라임이라고 할 때  
만일 도출해낸 X값인 X프라임과 실제 증명자가 가지고 있는 X값이 같다면  
e와 e프라임도 같게되어인증자는 x와 x프라임과 같다는 것을 납득할 수 있습니다.  
<br/>
<br/>
그 결과 인증자는 증명자의 시크릿키가 어떤 값인지 모르지만  
증명자가 시크릿키를 알고 있다는 것을 수학적으로 납득하게 됩니다.  
<br/>
<br/>
이 예제가 영지식증명의 조건을 만족하는지 보겠습니다.  
<br/>
<br/>
- Completeness  
: 어떤 조건이 참이라면, honest verifier는 honest prover에 의해 이 사실을 납득할 수 있다.  
=> X = X’ 가 참이라는 사실을 honest prover에 의해 납득할 수 있다.  

- Soundness  
: prover가 정직하지 못하다면, prover는 거짓말을 통해 verifier에게 조건의 타당함을 납득시킬 수 없다.  
=> prover가 private key를 몰랐을 경우, prover는 X = X’ 임을 납득시킬 수 없었을 것이다.  

- Zero-Knowledge
: 어떤 조건이 참일 때, verifier는 이 조건이 정확히 무엇인지 알지 못하여야 한다.  
=> verifier는 결국 prover의 private key를 알지 못한다.  
<br/>
<br/>
non interactive한 zkp가 간결하게 이루어질 수 있도록 한 것이 **zk-SNARKs**입니다.   
zk-SNARKs는 암호화폐거래시 거래 내역을 숨길 수 있기 때문에, 블록체인 분야에서 주목받고 있습니다.
<br/>
<br/>
zk-SNARKs는 보다 복잡하 개념들을 다루고 있기 때문에 다음 포스팅에 이어서 설명하겠습니다.


