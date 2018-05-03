# X.Blockchain Technical White Paper v1  
April 24, 2018  


*Copyright © 2018 XBLOCK SYSTEMS CO., LTD.*  
*Without permission, anyone may use, reproduce or distribute any material in this white paper for non-commercial and educational use (i.e., other than for a fee or for commercial purposes) provided that the original source and the applicable copyright notice are cited.*  

**DISCLAIMER:** This X.Blockchain Technical White Paper is for information purposes only. XBLOCK SYSTEMS does not guarantee the accuracy of or the conclusions reached in this white paper, and this white paper is provided "as is". XBLOCK SYSTEMS does not make and expressly disclaims all representations and warranties, express, implied, statutory or otherwise, whatsoever, including, but not limited to: (i) warranties of merchantability, fitness for a particular purpose, suitability, usage, title or noninfringement; (ii) that the contents of this white paper are free from error; and (iii) that such contents will not infringe third-party rights. XBLOCK SYSTEMS and its affiliates shall have no liability for damages of any kind arising out of the use, reference to, or reliance on this white paper or any of the content contained herein, even if advised of the possibility of such damages. In no event will XBLOCK SYSTEMS or its affiliates be liable to any person or entity for any damages, losses, liabilities, costs or expenses of any kind, whether direct or indirect, consequential, compensatory, incidental, actual, exemplary, punitive or special for the use of, reference to, or reliance on this white paper or any of the content contained herein, including, without limitation, any loss of business, revenues, profits, data, use, goodwill or other intangible losses.  

<br /><br />

**Abstract:** Bitcoin의 등장과 이를 이용한 거래의 급증은, 블록체인 기술이 거래 원장으로서 신뢰 할 수 있을 만큼 충분히 안전하다는 것을 증명하였다. 블록체인 기술이 주목 받게 된 주된 이유는, 기존 방식과 달리 신뢰성 확보 문제에 있어서 제3의 신뢰기관 (Trusted Third Party, TTP) 을 제거 하였다는 것과, 모든 거래 내역이 네트워크에 참여하는 모든 참여자들에게 분산 저장됨으로 거래 내용에 대한 조작이 사실상 불가능 하도록 만들었다는 점이다.  
블록체인 기술에서 가장 중요한 핵심 개념은 ‘Decentralization (탈 중앙화)’ 과 ‘Distributed Ledger (분산원장)’ 개념이다. 기존 방식은, 모든 거래가 하나의 집중화된 중앙 서버에 기록 되고, 해당 거래의 신뢰는 이 중앙서버 (제3의 신뢰기관) 에 의하여 ‘보증 되는’ 방식 이였다. 그러나 블록체인 상에서 발생한 거래는 네트워크에 참여하는 모든 참여자들에게 전달되어 ‘검증’, ‘합의’되고, ‘블록’ 단위로 묶여 순차적(선형적)으로 연결된다.  
모든 거래 내역이 기록 되는 블록체인의 크기는 누적 거래 건수가 증가 할수록, 즉 시간이 지날수록 점차 커질 수 밖에 없으며, 이는 네트워크의 모든 참여자가 전체 블록체인을 저장 관리 하는 것이 사실상 불가능해 지는 시점이 언젠가 도래함을 의미한다. 즉 블록체인 전체를 저장 관리 할 수 있을 만큼의 성능을 갖춘 시스템(노드)는 점차 그 수가 줄어 상대적으로 소수의 노드 집단을 이루게 될 가능성이 크다. 그리고 이 것은 또 다른 형태의 중앙 집중화의 결과를 가져올 것이다. 상대적으로 소수의 노드 집단이 전체 블록체인을 관리 하게 되는 상황에서 거래의 신뢰성은 이 소수의 노드 집단에 의존할 수 밖에 없게 된다. 즉, 블록체인의 근본 개념인 ‘탈 중앙화’ 가 심각히 훼손 될 수 있음을 의미한다.  
**본 문서는 특별히 전자문서의 보호를 위한 블록체인 기술 응용에 있어서,블록체인 연결구조를 기존의 선형적인 구조에서 다차원 구조로 변형한 X.Blockchain 을 제안 함으로서 전체 블록체인 크기 문제와 그로 인한 노드 집중화 문제에 대한 해결책을 모색 하고자 한다.**

<br />

<!-- MarkdownTOC depth=4 autolink=true bracket=round list_bullets="-*+" -->
## Table of Contrents

- [Problems](#problems)
- [X.Blockchain Structure](#xblockchain-structure)
  * [Block Header](#common-block)
  * [X.Block](#xblock)
  * [X.Transaction](#xtransaction)
- [Consensus Algorithm](#consensus-algorithm)
- [Inter Sub-chain Communication](#inter-sub-chain-communication)
- [Assets Model](#coin-model)
- [Accounts](#accounts)
- [Mining & Reward](#mining)
- [Development Roadmap](#development-roadmap)

<br />
<br />

## Problems

블록체인의 크기는 시간이 지날수록 거래의 누적 건수에 비례하여 지속적으로 증가할 수 밖에 없다. 블록체인 네트워크에 참여하는 모든 노드에 원장이 분산 저장 관리 되고, 이를 통해 제3의 신뢰기관 없이 거래에 대한 신뢰 확보가 가능하다는 블록체인 근본 개념에 충실하고자 할 때, 계속 증가되는 블록체인의 크기 문제는 거래 검증을 위한 노드의 참여에 있어서 한계 상황을 발생 시키게 된다. 즉 거대해진 블록체인을 저장 관리하는 완전노드[^완전노드]  로서 참여하기 위해서는 저장공간 확보와 같은 일정한 수준 이상의 성능이 요구된다. 이 성능 수준은 블록체인의 크기에 비례하여 지속적으로 상향 조정될 것이므로 참여 노드의 수적 감소는 불가피하게 되고 결, 또 다른 ‘중앙 집중화’ 형태로 귀결 될 수 밖에 없게 된다[^SPV]. 2017년 5월 현재 Bitcoin의 거래를 포함한 전체 블록체인 크기[^CHAINSIZE]는 이미 150G를 넘어섰고, Ethereum 의 블록체인 데이터는 최근 600G를 넘어섰다.

[^완전노드]: 완전 노드는 전체 블록체인을 저장하고 새로운 블록 채굴 작업을 진행하는 노드를 의미한다.
[^SPV]: 이 문제에 대한 해결책으로서 Bitcoin 에서는 SPV를 제안하고 있다. Transaction 데이터를 제외한 블록 헤더 정보만으로 거래 입증을 수행 함으로서 이에 필요한 리소스를 최소화 하는 방법이며 전자문서를 위한 블록체인 응용에서 이는 필수적으로 적용 된다. 암호화폐에서 transaction을 구성하는 것이 거래 내역 이라면, 전자문서 응용에서는 전자문서 데이터가 transaction의 주요 부분이 된다. 이 때 문서의 크기는 암호화폐의 거래 내역과는 비교할 수 없을 만큼 크기 때문에 블록체인에 문서 데이터 그 자체를 포함하지 않는다.
본 문서에서 특별한 언급이 없는 한 ‘블록체인 크기’는 ‘블록체인 헤더 크기’를 의미한다.
[^CHAINSIZE]: 거래를 기술하는 모든 Transaction 데이터와 블록 헤더 정보를 포함하는 전체 블록체인 크기.

<br />

<img src="images/blockchain_sz.png" width="480px" />

Figure 1. Bitcoin & Ethtereum blockchain size (출처:http://bc.daniel.net.nz/)

<br /><br />

<img src="images/cpu_pw.png" width="480px" />

Figure 1. Bitcoin & Ethtereum blockchain size (출처:http://bc.daniel.net.nz/)

<br /><br />

이러한 일정 수준의 '자격조건'은, 절대 다수의 사용자 클라이언트 (모바일 디바이스 포함)로 하여금 블록체인 네트워크에 완전 노드로서 참여 하는 것을 사실상 불가능하게 만든다. 결과적으로 사용자 클라이언트는 거래에 대한 신뢰 여부를 스스로 (제3의 신뢰기관 없이) 판단하지 못하고, 상대적으로 소수인 완전 노드 집단에 ‘의뢰’ 해야 만 하며, 그 결과를 일방적으로 ‘수용’ 해야 한다. 여기서 소수의 완전 노드 집단은 ‘제3의 신뢰기관’ 처럼 작동한다.
이런 ‘완전 노드의 집중화’ 문제의 배경에는 앞서 언급한 바와 같이 거대해진 전체 블록체인의 저장과 블록 생성(채굴) 시 요구되는 높은 computing power가 원인으로 자리 잡고 있다. 여기서 다시 전체 블록체인의 저장이 요구되는 이유는, 블록체인의 구조가 선형적인 연결 구조로 이루어져 있기 때문에 정작 필요한 블록들만 따로 떼어내는 것이 불가능 하기 때문이다. 중간 연결 관계가 끊긴 블록의 집합은 어떤 신뢰도 확인해 줄 수 없기에 아무런 가치를 갖지 못한다.
이러한 블록체인의 특성은, 그 것이 갖는 뛰어난 혁신성에도 불구하고, 여러가지 비효율을 감수하도록 한다. 예를 들어 어느 회사에서 내부 부서 활동 기록을 기록하여 관리하고자 어떤 공개 블록체인(Public Blockchain)을 활용하기로 결정하였다고 가정하자. 이 회사에서 운영하는 블록체인 노드에는 자신의 기록과는 상관없는, 전세계적으로 이 공개 블록체인 상에 발생되고 있는 수 많은 트랜잭션 전체가 저장되어야 한다. 그리고 이 전체 블록체인 데이터는 아마도(거의 확실하게) 이 회사가 발생시킨 트랜잭션보다 수 백, 수천 만배는 더 클 것이다. 이 회사 입장에서 보자면 불필요한, 그러나 자신이 발생시킨 데이터와는 비교도 안될 만큼의 큰 데이터를 저장 관리 해야 하는 것이다.
이러한 문제를 해결하기 위해 비공개 블록체인(Private Blockchain) 활용을 검토해 볼 수 있다. 물론 비공개 블록체인 역시 중요한 그리고 충분한 가치를 갖지만, 앞서 제기한 문제 이상으로 중앙 집중화된 구조를 갖는다는 점에서 우리가 바라는 해결책은 아니다.

우리는 공개 블록체인과 같은 수준의 분산 구조를 유지하면서, 하나의 단일한 블록체인 네트워크상에 제출(발생) 되는 모든 트랜잭션들을 유의미한 관계를 갖는 트랜잭션들의 집합을 구성하고, 각각의 집합에 포함된 트랜잭션들로 이루어진 독립적이고 개별적인 블록체인들의 연결 구조를 제안한다. 이로서 하나의 단일한 공개 블록체인 네트워크에 참여하는 모든 노드가 반드시 전체 블록을 관리해야 하는 제약 사항에서 벗어나, 선택적인 블록체인 구성 및 관리가 가능한 구조와 방법을 제시하고자 한다.

X.Blockchain 은, 발생되는 모든 기록(Transaction) 을 반드시 하나의 선형적인 구조로 구성하는 것을 강제 하지 않는다. 이는 트랜잭션에 따라 의도적으로 분기(fork) 를 허용함으로서, 유의미한 관계를 갖는 Transaction 들로 이루어진 개별적인 블록체인 구성이 가능함을 의미한다.
예를 들어 ‘문서’를 기준으로 하였을 때, 각 문서의 ‘최초 생성’ 은 기존 블록체인과 동일한 선형적인 구조의 블록체인(main-chain) 에 기록된다. 그러나 이미 main-chain에 기록된 특정 문서에 대하여 발생한 변경 등의 추가 기록(Transaction)은, main-chain이 아닌 main-chain 상의 해당 블록을 genesis block[^genesisblock]으로 하는 또 다른 블록체인인 sub-chain상에 기록 된다.
다시 앞서 제시한 예를 들자면, 공개 블록체인 상에 내부 활동 기록을 관리하고자 하는 회사에서는 이미 존재하는 main-chain 에 자신의 블록체인의 Gennesis Block 으로 사용될 블록을 생성하고, 그 블록을 시작으로 하는 별도의 sub-chain을 구성하여 여기에 회사 내부 활동 기록을 저장 관리 한다. 이로서 회사는 다른 목적을 갖는 트랜잭션 및 블록의 저장을 최소화 할 수 있다.

[^genesisblock]: 블록체인의 첫번째 블록.

<br />
<br />

## X.Blockchain Structure
* **Main-Chain**: 선형적인 블록연결 구조로 구성된 상위 블록체인으로 복수의 Sub-Chain을 가질 수 있다. Main-Chain 은 또 다른 상위 Main-Chain의 Sub-Chain일 수 있다.
* **Sub-Chain**: Main-Chain의 특정 블록을 Genesis 블록으로 하여 구성된 독립적인 블록체인. Sub-Chain은 또 다른 하위 Sub-Chain의 Main-Chain이 될 수 있다.
* **X.Block**: 블록체인을 구성하는 블록중 Sub-Chain 의 Genesis Block 역할을 하는 블록.
* **X.Transaction**: X.Block을 생성하기 위한 트랜잭션.
* **Full Node**: Main-Chain 과 전체 하위 Sub-Chain의 블록을 관리하는 노드.
* **Sub Node**: 특정 Sub-Chain의 블록만 관리하는 노드.
* **Blockchain Depth**: 노드가 관리하는 최상위 블록체인을 기준으로 관리 하고자 하는 Sub-Chain의 Depth.

X.Blockchain은 분기(fork)를 허용한다. X.Blockchain 상에서 분기가 발생하는 블록을 X.Block 이라 하며, 이 블록을 시작으로 분기되어 생성되는 (Sub-Chain) 의 genesis block 이 된다.
기존의 블록체인에서 분기는 블록에 기록된 데이터의 일관성(consistance)을 훼손 시킨다. 분기되어 같은 블록 높이(block height)를 갖는 복수의 블록이 존재한다는 것은 특정 시점에 서로 다른 상태가 동시에 존재함을 의미하는 것이다. 이는 A의 자산 총액(상태)이 m 일 수도 있고, n 일 수도 있다 라고 선언하는 것과 다름 아니다. 이를 해소하기 위해서 상태의 변화를 일으키는 사건은 반드시 순차적으로 처리되어야 한다. 상태를 변경시키는 임의의 사건에 대한 처리가 완료되어 상태 변경이 확정된 이후 다른 사건에 대한 처리가 이루어져 함을 의미한다. 예를 들어 A의 상태를 변경하는 사건을 t 라 하고, t에 의하여 변경된 A의 상태를 S(A)<sub>t</sub> 라 하자. 복수의 사건 t1 과 t2 를 가정하였을 때, 사건 t1 의 처리가 완료되어 A의 상태가 S(A)<sub>t1</sub> 으로 확정된 이후에 사건 t2 의 처리가 진행되어야 한다. t2가 선행하여 발생하는 경우도 마찬가지이다.

<br />

$$
\begin{array}{c}
S(A)_{t0} \xrightarrow{t1} S(A)_{t1} \xrightarrow{t2} S(A)_{t2} \\\\
or \\\\
S(A)_{t0} \xrightarrow{t2} S(A)_{t2} \xrightarrow{t1} S(A)_{t1}
\end{array}
$$

<br />

이것을 블록 구조로 표현하면 다음과 같다.

<br />
<center>
<img src="images/bc1.png" width="320px"/>
<br /><br /> or <br /><br />
<img src="images/bc1-2.png" width="320px" />
</center>
<br />

그러나 S(A) 에 대하여 t1 의 처리가 완료되기 이전에 t2 에 대한 처리가 동시에 이루어진다면, 사건 t1 이 처리되는 시점의 A의 상태를 S(A)<sub>t0</sub> 라 할 때, 사건 t2 가 처리되는 시점의 A의 상태 역시 S(A)<sub>t0</sub> 가 되므로, S(A)<sub>t0</sub>는 S(A)<sub>t1</sub> 와 S(A)<sub>t2</sub> 두가지 상태로 분기된다. 이는 전형적인 이중 지불 문제가 발생한 경우이며, 이 문제를 해결하기 위해서는 특정 시점(block height = #n-1) 에서 A의 상태는 오직 하나로 강제 되어야 한다.

<br />
<center>
$$
\left .
\begin{array}{c}
S(A)_{t0} \xrightarrow{t1} S(A)_{t1} \\\\
S(A)_{t0} \xrightarrow{t2} S(A)_{t2}
\end{array}
\right \} \space \text{What is real ?} \space\space S(A)_{t1} \space\space or \space\space S(A)_{t2}
$$
</center>
<br />



<br />
<center>
<img src="images/bc1-3.png" width="320px" />
</center>
<br />

즉 S(A)의 최종 상태는 S(A)<sub>t1</sub> 과 S(A)<sub>t2</sub> 둘 중 어느 하나로 결정되어져야 하며, 이로 인해 결과적으로 사건 t1, t2 중 어느 하나는 버려져야 함을 의미한다.

<br />
<center>
<img src="images/bc1-4.png" width="360px" />
</center>
<br />

이는 동일한 대상의 상태 변경을 일으키는 모든 사건은 동시성을 가질 수 없고 순차적인 처리가 강제 되어야 하고, 이로 인해 사건들을 포함하는 블록과 블록의 연결구조는 선형적인 형태의 구조로 될 수 밖에 없음을 잘 설명하여 준다. 반면에 각각의 사건들로 인하여 상태 변환이 발생하는 대상들이 모두 동일하지 않다면 해당 사건들은 '반드시' 순차적으로 처리 되어야 할 필요는 없다. 이 경우 각 사건들의 처리가 순차적으로 강제되지 않아도 앞서 언급한 이중 지불 문제와 같은 모순은 발생되지 않을 것이다. 사건의 순차적인 처리는 동일한 대상의 상태 변환을 일으키는 사건들 사이에서만 의미를 갖는다.  
이것은 n개의 서로 다른 대상의 상태를 변화 시키는 사건 집합 각각을 T<sub>n</sub> 이라 할 때, T<sub>1</sub> ~ T<sub>n</sub>을 n 개의 독립적인 선형 구조로 각각 구성하는 것이 가능함을 의미한다. 즉 동일한 대상을 갖는 특정 사건 집합 T 내에서만 순차성이 보장된다면, 서로 다른 사건 집합 T<sub>n</sub> 과 T<sub>m</sub> 에 속하는 사건 t<sub>n</sub> 과 t<sub>m</sub> 은 순차적으로 처리되어야 할 필요도, 동일한 직렬화 구조에 포함될 필요도 없다.

X.Blockchain 은 사건 집합 T<sub>n</sub> 으로 이루어진 개별적이고 독립적인 블록체인, Sub-Chain 을 구현한다.
Sub-Chain 을 구성하는 모든 트랜잭션(사건)과 블록은 Main-Chain 또는 다른 Sub-Chain 상의 트랜잭션 및 블록과 어떤한 인과관계를 갖지 않는다. 각각의 Sub-Chain 에 적용되는 모든 메커니즘은 Sub-Chain 상에서 또 다른 X.Block 을 생성하는 것을 포함하여 Main-Chain 상의 그것과 완벽히 동일하다. 때문에 Sub-Chain 그 자체로 하나의 완전한 블록체인으로 기능할 수 있다.


<br />
<center>
<img src="images/xbc1.png" width="480px" />
</center>
<br />


#### X.Block
- Sub-Chain 이 생성되는 블록, 즉 Sub-Chain 의 genesis block 이자, Main-Chain 의 특별한 블록을 X.Block 이라 한다. 특정 X.Block 으로 부터는 오직 하나의 Sub-Chain 만이 생성된다. 그러나 Sub-Chain 은 연결되는 블록중에 또 다른 X.Block 을 포함할 수 있어 다차원적인 블록체인 구조가 가능해 진다.
- 각각의 Sub-Chain 은 그 상위의 Main-Chain 과는 독립적인 자산을 갖는데, 이 자산의 기본적인 특성은 해당 X.Block 을 생성하는 X.Transaction 에 기술 된다.
- X.Block 은 단 하나의 X.Transaction 을 포함한다.

- X.Block 은 이전 X.Block 의 해쉬를 포함하고, 현재 X.Block 의 해쉬는 다음 X.Block 에 포함된다. 때문에 X.Block 은 다른 블록들과는 달리 복수의 해쉬 연결 구조를 갖는다.

- Block Header

#### X.Transaction
X.Block 을 생성하기 위한 특별한 트랜잭션을 X.Transactioin 이라 한다. X.Transaction 의 발행을 시작으로 X.Block 이 생성되고, 이 X.Block 으로 부터 Sub-Chain 이 생성된다.

- X.Transaction 에는 이로 인하여 생성될 Sub-Chain 의 기본적인 특성을 기술하는 내용으로 구성된다.

- Transaction 이 포함되어 기록되어야 할 Target ChainID를 갖는다.

- X.Transaction 생성자(계정)
- Sub-Chain 이름
- Sub-Chain 자산 이름
- Tx. fee


X.Transaction 은 보통의 Transaction 보다 높은 비용을 소비한다. 이는 불필요한 X.Transaction 의 생성을 방지하여 X.Block 및 Sub-Chain 의 과다한 발생으로 인한 전체 블록체인의 효율성을 높이기 위함이다.

- X.Transaction 은 생성되는 Sub-Chain 의 특성을 기술한다.
- X.Tx confirmation ????
- X.Tx 비용


#### Common Block Header

- X.Block 은 Main-Chain 상에서의 블록 번호를 가지며 이 번호는 해당 X.Block 으로 부터 생성되는 Sub-Chain 을 식별하기 위한 ChainID 로 사용된다. 위 그림에서 X.Block 의 블록번호 \#4 는 Main-Chain 상의 block number 에 해당되며 동시에 ChainID \#4 를 의미한다. ChainID \#4 가 가리키는 Sub-Chain 의 블록은 {ChainID}.{BlockNo} 형식의 블록 번호를 갖는다.

```
{
  blockNo:1234.5678
  blockHash: "0xABCDEF0123456789"
  preBlockHash: "0xABCDEF0123456789"
  preXBlockHash: "0xABCDEF0123456789"
  version:XB1
  stateHash: "0xABCDEF0123456789"
  transactions:[]
}
```

- State Hash (Petricia Merkle Tree's Root Hash)


## Consensus Algorithm
#### PBFT
#### DPOS
#### PBFT\+DPOS
#### POX
*POSO? - singing order*
*validator 선정 방법 - 분산성, 임의성*
*Casper, Tendermint*

## Coin Model

*sub-chain 별 coin 가짐*
*double hash link 설명*
*coin 간 환전 (inter sub-chain communication)*
*채굴, tx fee (for common, joint block)*

## Mining

## Accounts
X.Blockchain 계정의 지출액과 잔고를 기록하기 위해 회계 모델을 도입해야 한다. X.Blockchain이 채택하는 모델은 이더리움 yellow paper에 기술되어 있는 머클 패트리샤 트리(Merkle Patricia Trie) 데이터 구조이다[35]. 일련의 트랜잭션이 발행되면 모든 트랜잭션 이력을 하나의 해시값으로 저장하고 있는 월드 스테이트가 업데이트 되고 그 값은 각 블록에 저장된다. 이 월드 스테이트 값은 모든 하이콘 계정의 계정 데이터를 나타내는 머클-패트리샤 루트의 해시값이다.
계정 데이터에는 특정 하이콘 계정의 잔고, 그 특정 계정을 참조하는 가장 최근 블록에 대한 레퍼런스, 그 특정 계정에서 시작된 트랜잭션 수를 나타내는 넌스값이 저장되어 있다. 넌스값은 리플레이 공격에서 데이터를 보호하는 데 사용된다. 이전 블록에 대한 레퍼런스는 트랜잭션 이력을 더욱 빨리 검색할 수 있게 해주고 스펙터 알고리즘에서 이중지불을 쉽게 추적할 수 있도록 해주는 최적화된 기능이다.

*ethereum's patricia merkle tree*
*tx와의 관계? -> state hash root <- block*



## Development Roadmap



## Effectiveness
<center>
<img src="images/image002.png" width="450px"/>
<br />
Figure 2. X.Blockchain
</center>
<br />



위 그림은 2차원 구조로 구성된 X.Blockchain 의 모습이다. 앞서 언급한 ‘문서’를 main-chain의 기준으로 잡았을 때, main-chain 을 구성하는 각각의 블록(B<sub>00</sub> \~ B<sub>n0</sub>)은 모두 신규 문서의 생성 기록을 포함하며, 동시에 각 sub-chain의 genesis block 이 될 수 있다. 예를 들어 B<sub>20</sub> 에 생성이 기록된 전자 문서 E<sub>20</sub> 에 대한 최초 수정이 발생하였다면 이는 main-chain 상의 다음 블록인 B<sub>30</sub>이 아닌, B<sub>20</sub> 을 genesis block 으로 하는 sub-chain 상의 B<sub>21</sub>에 기록 된다.

 

 

<center>
<img src="images/image003.png" width="450px"/>
<br />
Figure 3. 선형적인 블록체인
</center>
<br />

선형적인 블록체인 구조에서는 동일한 문서라 할 지라도 해당 문서의 변경 등의 추가 기록은 블록체인의 추가 블록을 필요로 한다.

위 그림은 문서 D<sub>0</sub> \~ D<sub>3</sub> 가 블록체인에 추가된 상황을 선형적으로 구성한 예이다. 이 예에서 문서 D<sub>n</sub>은 생성, D<sub>n-m</sub> 은 문서 D<sub>n</sub>에 발생한 변경, 전송 등의 추가 기록을 의미한다. 문서 D<sub>0</sub> 의 경우 생성을 포함하여 총 6개의 기록 (\~ D<sub>0-5</sub>)이 발생하였고, 문서 D<sub>2</sub>의 경우 총 3번의 기록 (\~ D<sub>2-2</sub>) 이 발생하였다.

위와 같은 선형적인 블록체인에서 모든 클라이언트는 특정 문서의 신뢰를 스스로 검증하기 위해서 전체 블록 모두를 확보하여 저장하여야 한다. 즉 문서 D<sub>2</sub>만 필요한 클라이언트라 할지라도 사실상 불필요한 D<sub>0</sub>, D<sub>1</sub>, D<sub>3</sub>,에 대한 블록과 D<sub>0</sub>의 추가 기록을 담은 블록 D<sub>0-1</sub> ~ D<sub>0-5</sub>까지 총 11개의 블록이 필요하다.

 

그러나 동일한 상황을 X.Blockchain 으로 구성하게 되면 다음과 같아진다.

 

 


![](images/image004.png)
Figure 4. X.Blockchain

 

 

위 그림에서 B<sub>00\\ \~</sub>B<sub>30</sub> 은 main-chain 을 구성하고, B<sub>00</sub>와 B<sub>20</sub>를 genesis block으로 하는 각각의 sub-chain이 구성되어 있다. 이와 같은 다차원 구조의 블록체인에서는 모든 클라이언트가 전체 블록체인을 가질 필요가 없다. 앞서 예에서와 같이 문서 D<sub>2</sub>만 필요 하다면 전체 블록체인이 아닌 D<sub>2</sub>를 genesis block으로 하는 sub-chain 과 그 상위 main-chain만 보유하면 문서 D<sub>2</sub> 에 대한 신뢰여부를 스스로 판단 할 수 있다. 즉 main-chain 의 블록 B<sub>00\\ \~</sub>B<sub>30</sub>과 sub-chain의 블록 B<sub>21\\ \~</sub>B<sub>22</sub>로 구성된 총 6개의 블록정보를 보유하는 것으로 충분한 것이다.

 

여기서 Main-chain에 적용된 ‘분류 기준’이 반드시 ‘문서’가 되어야 하는 것은 아니다. 서비스의 구성에 따라 ‘부서’ 단위가 기준으로 적용될 수 도 있고, 또는 연관성을 갖는 문서의 집합이 분류 기준으로 적용될 수도 있다.

또한 상황에 따라서 sub-chain은 또 다른 sub-chain의 main-chain이 되도록 구성이 가능하다. 토지대장의 경우, 큰 범위의 ‘지역’을 main-chain의 기준으로 하고 그 보다 작은 단위인 시,군,구 단위를 1차 sub-chain으로, 그리고 토지 구분 단위를 2차 sub-chain으로 분류한다면 위 그림에서 보여지는 2차원 구조가 아닌 아래 그림과 같은 3차원 구조의 구성이 가능하다.

 

 


![](images/image005.png)
Figure 5. 3차원 구조의 X.Blockchain

 

 

X.Blockchain 상에서 새로운 블록 채굴을 담당하는 완전노드는 여전히 모든 블록에 대한 정보를 가져 한다. 그러나 채굴이 아닌 문서에 대한 신뢰 여부를 판단하는데 있어서 클라이언트 (사용자 기기) 는 전체 블록을 가져야 할 필요가 없다. 각각의 사용자는 검증이 필요한 문서가 포함된 sub-chain과 바로 상위 단계의 main-chain을 확보 하는 것으로 충분히 해당 문서에 대한 신뢰를 ‘제3의 신뢰기관’ 없이 검증할 수 있다. 여기서 완전 노드는 신뢰여부를 확인해 주는 제3의 신뢰기관이 아닌 ‘채굴’을 수행하는 역할을 할 뿐이다.

 

 

 

 

**Parallel Transaction Processing**

 

선형적인 블록체인 구조에서는 하나의 블록이 생성되는 동안 발생한 모든 거래(Transaction)가 다음 블록 채굴이 시작될 때까지 대기상태로 머물게 된다. 더욱이 하나의 Block 크기[^blocksz]가 무한정 커지는 것이 허용되지 않는 만큼 경우에 따라서는 다음 블록이 아니라 그 다음 블록 채굴 때까지 대기해야 하는 경우도 충분히 발생 가능하다. 블록체인 네트워크에 배포된 transaction이 블록에 포함되어 확정 되기까지의 대기 시간은 ‘초당 Transaction 처리 수’ - TPS[^tps]를 떨어뜨리게 되어 그 만큼 transaction의 처리는 지연 된다.

[^blocksz]: Bitcoin 에서 블록 크기는 1MB로 제한되고 있다. 블록 크기 제한은 블록이 포함할 수 있는 거래의 수를 제한 하게 되어 단위 시간당 처리되는 거래의 수 (Transaction Per Seconds - TPS) 를 떨어뜨린다. 현재 Bitcoin의 블록 크기를 확장하는 문제가 논의 중에 있으며 Bitcoin Classic 과 같은 일부 구현에서는 2MB의 블록크기를 사용하고 있기도 하지만 채굴자들의 지지를 받지 못하고 있다.
[^tps]: 현재 Bitcoin의 TPS는 약 7 TPS 정도를 기록하고 있다.

그러나 X.Blockchain 상에서는 각 sub-chain 별로 블록 채굴 작업의 동시 진행이 가능하다. 예를 들어 main-chain 상의 블록 B<sub>n0</sub> 채굴 작업과 sub-chain 상의 블록 B<sub>2m</sub> 채굴 작업은 서로 동시에 진행될 수 있다. B<sub>n0</sub>과 B<sub>2m</sub>는 서로 분리된 블록체인에 속하므로 상호 연결 관계를 갖지 않기 때문이다.

 

 


![](images/image006.png)
Figure 6. Transaction Processing - 선형구조 블록체인

 

 

위 그림은 앞서 제시된 예시 상황에서 D<sub>4</sub>가 신규 생성되고 뒤 이어 문서 D<sub>1</sub>과 D<sub>3</sub>에 대한 변경 기록이 발생하였을 경우를 가정한 그림이다. 선형적인 블록체인에서는 D<sub>1</sub>과 D<sub>3</sub> 변경 기록은, 진행중인 D<sub>4</sub> 신규 생성에 대한 채굴 작업이 진행되는 동안 대기상태에 머무르게 된다. D<sub>4</sub> 생성 블록 B<sub>11</sub>의 채굴 작업이 완료된 후, 다음 블록 B<sub>12</sub>에 대한 채굴이 시작될 때 D<sub>1</sub>과 D<sub>3</sub>에 대한 변경 사항은 블록 B<sub>12</sub>에 포함되어 채굴 작업이 완료되어 최종적으로 블록체인에 연결 될 때 비로소 블록체인에 기록이 완료된다.

 

 


![](images/image007.png)
Figure 7. Transaction Processing – X.Blockchain

 

그러나 다차원 구조의 X.Blockchain에서는 위 그림과 같이, 문서 D<sub>4</sub> 생성 블록 B<sub>40</sub>의 채굴이 완료될 때까지 문서 D<sub>1</sub>, D<sub>3</sub>의 변경 사항에 대한 블록 B<sub>11</sub>, B<sub>31</sub>의 채굴 작업이 대기 할 필요가 없다. 제시된 예에서 모든 신규 블록 B<sub>40</sub>, B<sub>11</sub>, B<sub>31</sub>에 대한 채굴 작업은 각각 독립적으로 동시 진행이 가능하다.

** **

 

 


**Effectiveness**

 

주민등록초본을 전자문서화 하고 블록체인 기술을 적용 하여 관리 하는 상황을 가정하였을 때, X.Blockchain 적용과 기존 블록체인 적용이 어떻게 다른가에 대하여 설명한다.

구성 인구 1명당 1개의 초본이 존재하고 이는 다시 1개의 블록을 구성한다고 가정하였으며, 해마다 이동 인구수 만큼 주민등록초본 갱신이 발생하고 이 역시 하나의 블록으로 기록됨을 가정하였다.

 

 


| | |2016년|
|---|---|---:|
|총이동|이동자수|7,378 명|
|    |이동률(%)|14.4%|
|    |전입신고건수|14.4%|
|    |이동률(%)|14.4%|
*[단위:천명, %, 천건], 출처:통계청 「국내인구이동통계」*


대한민국의 총인구는 국가통계포털([http://kosis.kr](http://kosis.kr)) 발표에 따르면 2015년말 기준 51,525,338명 이다. 인구 1명당 주민등록초본 1부가 존재하고 거주 지역을 이동 할 때 마다 이 초본은 갱신되어야 하며, 위 표의 데이터에 따르자면 2016년 한 해 동안 총 7,378,000번[^총인구]의 초본 갱신이 발생 되었음을 알 수 있다.

[^이동인구]: 국가통계포털의 발표 자료에 따르면 2016년 정확한 이동인구의 수는 7,378,383명이다.

이를 선형적인 블록체인으로 구성한다면, 최초 블록체인은 전체 인구수 만큼의 블록으로 구성되고, 해마다 인동 인구수 만큼의 블록이 추가되어야 한다. 만약 2016년부터 적용한다면 2016년 말 기준으로 블록체인의 블록 수는 아래와 같다.


| $ 51,525,338 + 7,378,000 = 58,903,338 $ |
|---|

그리고 한 해 평균 7,000,000 명이 이동한다 가정 하였을 때, 해마다 7백만개의 블록이 추가된다. 여기에 한 블록당 80byte의 크기[^blockheadersz]로 하여 10년 동안의 기록이 누적된 전체 블록체인 크기를 산출하면 아래와 같다.

[^blockheadersz]: Bitcoin의 블록헤더 크기는 81 byte 이다.

| 블록체인의 크기 = $ (51,525,338 + 7,000,000 * 10) * 80 / 1024^3 = 9.1 G $|
|---|

 

 

즉 선형 구조의 블록체인의 경우, 10년동안의 누적 블록체인 크기 9.1G 와 향후 매년 변경 증가분 0.52 G 가 선형적으로 증가한다.

 

동일한 조건을 X.Blockchain에 적용하면, 전체 블록의 수와 크기는 동일하지만, 해마다 추가되는 변경 블록수가 main-chain에 선형적으로 연결되는 것이 아닌 sub-chain으로 구성 될 것이다. 즉 10년 동안의 변경분에 대한 70,000,000개의 블록은 51,525,338 개의 블록으로 구성된 main-chain의 sub-chain 으로 분산되어 구성될 것이다. 변경분의 블록이 main-chain의 sub-chain에 분산되는 정도를 단순 산술 평균으로 적용하면, main-chain의 블록 1개당 1개의 sub-chain을 갖고, sub-chain당 1.35개[^subchainblockcount] 의 블록을 갖게 된다. 이에 근거한 인구 1명당 블록체인의 크기는 아래와 같다.

[^subchainblockcount]: 인구 1명당 10년동안 평균 이동 수에 해당한다.

| sub-chain의 평균크기  = $ (7,000,000 * 10 / 51,525,338) * 80 = 108.68 B $ |
|---|



| main-chain의 크기 = $ 51,525,338 * 80 / 1024^3 = 3.83 G $ |
|---|






선형 구조의 블록체인과는 달리 X.Blockchain 의 경우, 필요한 데이터에 대한 선별적 관리가 가능하다. 만약 어떤 이유에 의하여 특정 인구 1백만명에 대한 주민등록초본관리 등과 같은 서비스가 가능하다는 것이다. 이 경우 인구 1백만명의 10년동안의 주민등록초본 변경 이력에 대한 검증을 위해서 필요한 총 저장 용량은 다음과 같다.


| $ 3.83 + 108.68 * 1,000,000 / 1024^3 = 3.93 G $ |
|---|


향후 블록체인의 크기는 해마다 1백만명에 대한, 한해 평균 변경 블록 크기 만큼만 증가하게 된다.

이 장에서 가정한 상황에는 출생에 따른 main-chain의 증가와 사망, 혼인, 이혼및 기타 사유에 따른 sub-chain의 변경(증가)[^제외된증가분]에 대해서는 고려하지 않았다. 때문에 실제로는 더 큰 전체 블록체인이 필요할 것이다. 또한 main-chain의 분류 기준이 반드시 인구 1명이 되어야 하는 것도 아니며, 하나의 블록에 반드시 하나의 문서(Transaction)만 포함되어야 하는 것도 아니다. 때문에 위 산출 값은 실재적 의미가 아닌, 선형 구조의 블록체인과 다차원 구조의 X.Blockchain 비교를 위한 상대적인 값으로서의 의미만을 갖는다.

[^제외된증가분]: sub-chain의 증가분이 클수록 다차원 구조 X.Blockchain의 효율성은 높아진다.

다만, 위 예시는 주민등록초본 이라는 문서 1종에 대해서만 적용한 경우인데, 실제 다양한 공공문서가 동시에 적용될 수 있을 것이다. 추가되는 문서가 많을수록 선형적인 블록체인 대비 다차원 블록체인의 상대적 효율성은 급격히 커지게 된다. 만일 위 예시에 다른 공공기관 제증명 문서 1종이 추가되면, 그리고 문서 변경 이력 등과 같은 Transation이 주민등록초본과 비슷한 비율로 발생된다 가정하면, 선형적인 블록체인의 경우 문서 1종 추가시 약 2배의 블록이 추가적으로 필요해 진다. 그리고 또 다른 문서가 추가된다면 역시 마찬가지로 동일한 블록의 증가가 발생한다.

그러나 X.Blockchain의 경우, main-chain 의 크기에는 변화가 없으며, 추가된 문서 종류에 의한 블록 추가는 모두 sub-chain상에서만 이루어지게 되므로, 다차원 구조의 X.Blockchain의 상대적 효율성은 그만큼 높아지게 된다.

 

 

 




**Conclusion**

언급한 바와 같이 전체 블록체인의 크기는 달라지지 않는다. 다차원 구조의 X.Blockchain의 주요 차별성은 특정 기준에 따른 데이터(블록)의 선택적 관리가 가능 하다는 점에 있다. 그리고 사용자 클라이언트가 필요한 범위의 블록체인을 선택적으로, 직접 저장 관리 함으로서, 해당 범위 내에서 만큼은 문서에 대한 신뢰의 문제를 제3의 신뢰기관의 개입 없이 스스로 해결 할 수 있도록 하는 데 그 목적이 있다.
