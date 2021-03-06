## 背景知識 I 運算放大器

---

### 理想的電路元件

執行電訊號處理的元件或系統為電路元件或電路系統。理想的電路元件像優秀的軍人一樣，對上級的命令絕對服從，對下級的要求貫徹到底。 軍隊的目標在於達成作戰目標，而做戰目標常是由許多個小行動組成，每一個動作都要確實，才能互相配合達到目標。如果下級對上級的命令可以打折扣則每個命令必須依據折扣的大小提高要求，而這折扣及要求又會因人而異，結果是指揮官必須要了解到隊中每一個人的特性及相互關係才能擬定作戰計劃，對三五個人的小隊或許可能，但對一個上百人的部隊則不可能。  

每個電路均是由許多元件所組成，其組合的目的在於訊號的傳遞或處理。而訊號在電路中先到的部份稱為前級就如同軍隊的上級，而訊號較晚到的部份為後級就像是下級。如果每一級的電路均不會因上一級或下一級的電路而改變其電路特性的話，那在設計電路時僅需先個別設計每一個單獨一級的電路，而後將電路按前後級接起來即可，不需要將整個電路一起考慮設計，如此才有可能設計一個很大的電路。 這種設計觀念稱為塊狀化，塊狀化的目標在於使每個功能方塊均能獨立(Decouple)。  IC是這種觀念的產物，近年來物件導向程式是這種觀念，而機械領域中零組件標準化也是這種觀念。
數位訊號元件較類比訊號元件的服從性高，雖然數位訊號的類比特性(電流,電壓),會因為上下級電路的不同而改變，但是由於，數位的1及0 各有其類比特性變化的容許範圍，如TTL 以為1，而以V為0，因此數位特性上不易變 化，只要按規格設計使用正常零件，數位特性是可以保證服從的。這也是為什麼數位電路的設計可 以做到比類比的更複雜的原因。  

### 理想電壓類比電路

除了頻率，波寬調變訊號之外。電壓，電流類比訊號元件，基本上後級對於前級類比訊號並不會完全服從，而會有前級會受到後級影響改變訊號，使最終前後級間訊號是由前後級共同決定的狀況。  

![電壓類比訊號標準功能方塊分析](./img/5-1.png)  
圖5-1 電壓類比訊號標準功能方塊分析，電導傳遞放大器，Transconductance Amplifier

圖5-1 電壓類比訊號標準功能方塊，所示為類比電路以電壓訊號分析示意，電路中如由 $$R_1$$ 及 $$E$$ 端向右看，等效電阻為 $$R_{12} = \frac{R_2 R_3}{R_2 + R_3}$$ (第二級輸入阻抗)，如由端向左看依Thevenin's Thm，等效電壓源為 $$ E_{O2} = E \frac{R_2}{R_1+R_2} $$ (第二級電壓源)，等效電阻為 $$ R_{O2} = \frac{R_1 R_2}{R_1 + R_2} $$ (第二級輸出阻抗)。為 $$ R_{i2} $$ 第一級阻抗負荷(第二級輸入阻抗)， $$R_{O2}$$ 對第三級而言為電壓訊號源串聯內阻抗。只是負荷電阻 $$R_{i2}$$ 與第三級 $$R_3$$ 有關，而電壓 $$ E_{O2} = E \frac{R_2}{R_1+R_2} $$ 與輸出阻抗 $$R_{O2}$$ 又與第一級的電阻 $$R_1$$ 相關，如此一來三個級之間會偶合在一起無法獨立分析。上述的分解方式以西分寧等效為核心，每一級均以電壓訊號為分析標的，是電壓類比訊號標準的電路分析。由於每一級的輸入訊號是輸入阻抗上的跨壓，亦可以說是正比於輸入電流，而輸出訊號是電壓。因此每一級增益亦可視為一個電導，即輸 出電壓為輸入電流的倍數，故這種分析方式又名電導傳遞放大器(Transconductance  Amplifier)。  

在電壓類比訊號分析中，若希望各級之間，呈現出，前級訊號不受後級影響的理想電路。需要輸入阻抗為無限大，或則是輸出阻抗為零。如本範例，若第二級輸入阻抗為無限大則第一級無論內電阻為何均不影響輸入端的電壓，因此第二級的增益便不會隨第一級的內阻而變。若第二級輸出阻抗為零，則可以讓輸出到第三級的電壓訊號為第二級電壓源訊號，不受第三級輸入阻抗分壓影響。也就是說，電壓訊號標準電路功能方塊，會是由一個做為前級負載的輸入阻抗，以及一個西分寧電源串聯輸出阻抗共同組成，而理想的電壓訊號標準電路功能方塊，則更進一步，需要有無限大的輸入阻抗，零輸出阻抗，以及可以任意調的增益$$\alpha$$。

### 回饋控制及運算放大器Operation Amplifier OP AMP

運算放大器是實現回饋控制類比電路的核心元件，運算放大器在類比電路中的地位就如同CPU在數位電路中。而回饋控制則是類比電路用以克服製造誤差達成高精密度的最重要理論基礎。假設圖5左邊所示為一個系統，其誤差E受到輸入$$X$$，系統特性$$G(s)$$，以及環境雜訊$$n$$影響共同決定$$E=G(s)X+n$$。當這個誤差被用高增益負回饋進入輸入如圖5-2右邊所示。則由於加入了回饋的關係式，可以推導出如下的結果。

$$ E = G(s) X - G(s) K_p E + n $$  
$$ ( 1 + G(s) K_p ) E = G X + n $$  
$$ E = \frac{ G(s) }{ 1 + G(s) K_p } X + \frac{ 1 }{ 1 +G(s) K_p } n
\approx 0 , as  K_p \approx \infty $$  


由於$$K_p$$會出現在式子中的分母，當他很大時，不論雜訊大小，以及系統特 性如何均會讓誤差逼近於0，

![高增益回饋控制壓抑誤差提高精度](./img/5-2.png)  
圖5-2 高增益回饋控制壓抑誤差提高精度

運算放大器是一個設計來實現高增益回饋控制用的電壓類比訊號處理標準元件。因此它除了滿足，高輸入阻抗，低輸出阻抗，輸入電流(即差動電壓)控制電壓源的標準電壓類比元件特性之外，他更進一步提供了極高的差動電壓放大增益值，以備執行高增益回饋控制使用。運算放大器(Operation Amplifier)的得名即來自它可以配合不同的需求，以高增益回饋方式來實現各種數學運算(Operation)，其中包括加，減，乘，除及微分，積分等。就如同CPU的出現將原來複雜多變的邏輯電路系統化簡單化為單一IC，提供設計者以程式規劃不同的用途。運算放大器的發明則提供了單一一顆IC，配合設計者以不同的電阻電容網路回饋方式將運算放大器規劃成不同用途以取代原來複雜的類比電路設計。

### 理想運算放大器
由於製造以及設計技術的成熟，現在的運算放大器，若能依照規格使用，且精度需求只在一般等級時，已經可以忽略運算放大器的不完美。亦即，我們可以把常用的運算放大器視為理想運算放大器來使用，而不擔心其非理想所造成的誤差影響結果。想理解運算放大器最好的方法是直接把它當成像電阻，電容等天生的元件一樣，物理特性已定，設計者只要照特性使用不必管它內部組成。圖6為理想的運算放大器等效電路圖，輸出的電壓位準正比於輸入兩端電位差，且輸出阻抗極小，輸入阻抗極大。

![理想運算放大器等效電路](./img/5-3.png)  
圖5-3 理想運算放大器等效電路

理想運算放大器原始特性
  1. 差動放大增益無限大
  2. 輸入阻抗無限大
  3. 輸出阻抗為零
  4. 頻寬(Band Width)為無限大

理想運算放大器的原始特性在電路分析的時候並不容易使用，利用前二項特性可以導出以下衍生特性， 對於使用運算放大器的電路分析非常有用:
  1. **輸入兩端電壓差永遠為零。**
  2. **流入兩輸入端點電流均為零。**  

其中第一個特性是因為輸出電壓有限，就只能在正或負電壓源得到電壓，而理想差動放大增益無限，反推差動電壓$$V_{+-}$$是有限值除以無限值得到零電壓差。

$$V_{+-} = lim_{A_{vo} \to \infty} \frac{ V_{out} }{ A_{vo} } $$

而第二個性質是因為輸入阻抗$$r_{in}$$無限大阻止任何電流流入

$$ i_+ = - i_- =i_{in} = lim_{ r_{in} \to \infty } \frac{ V_{+-} }{ r_{in} }$$

### 真實運算放大器模型
真實的運算放大器是由電路所組合而成，電路的設計製作目標是能達成理想運算放大器的性能，但實際上由於元件及電路製作上的限製一定會與理想有一些差距,這些差距會造成實際使用運算放大器時的誤差。圖5-4為實際運算放大器的等效電路


![實際運算放大器的等效電路圖](./img/5-4.png)  
圖5-4 實際運算放大器的等效電路圖

實際運算放大器的等效電路圖中 $$Z_{diff}$$ 表示輸入阻抗不為無阻大。另外還增加了一對 $$Z_{cm}$$ ，由 $$Z_{diff}$$ 兩端接地，共同組成電抗網路，此一來當實際放大器的 兩端輸入相同大小的電壓訊號時，也可能造成理想放大器的兩端有電壓差，並放大。 這代表的是，實際運算放大器不但對於差動訊號會放大，對於共模訊號也會放大。  

此外在實際的放大器 +,- 輸入接點之間，有兩個電壓源$$V_{iOS}$$、$$V_n$$， 其中 $$V_{iOS}$$ 為輸入偏離電壓(Input Offset Voltage)，代表實際上放大器內部電路可能不平衡而造成，就算實際放大器的 +,- 輸入沒壓差，理想運算放大器的 +,- 兩端電壓差也不等於零，並且放大影響輸出電壓的精確度。而則代表內部電路的交流電壓雜訊。  

實際運算放大器兩端點至地之間除了有接地共模阻抗之外，也另有洩放電流，其中直流部份為 $$I_b+$$ 、 $$I_b-$$ 而交流部份則為 $$I_{n+}$$、$$I_{n-}$$ 。 $$I_b+$$ 、 $$I_b-$$ 名為輸入偏差電流(Input Bias Current)，正負端輸偏差電流且若適當設計輸入訊號，其造成的不良效應可以相互抵消，剩下無法完全扺消的差異部份，名為輸入偏離電流(Input Offset Current)。  

$$I_b+$$ 、 $$I_b-$$ 各自流乘上輸入端點至地的等效阻抗，會得到 +,- 輸入端的電壓提昇。 假如外部電路 +,- 兩端到地等效阻抗不同，相同的偏差電流也會造成不同的電壓提昇，而使理想運算放大器的 +,- 輸入端有電壓壓差，進而影響輸出精度。為避免這種狀況發生，使用運算放大器來設計各式放大電路時，應注意調節電阻阻值，**使 +,- 兩端至地的等效電阻相近** 。  

$$I_{n+}$$、$$I_{n-}$$ 則為溫度及高頻干擾等各種因素所造成的交流電流雜訊。

標準的運算放大器，其頻率增益曲線如圖5-5，可以描述為一個單一Pole的線性系統(內部相位補嘗型)，或是兩個Poles的線性系統(無內部補嘗)。由基本控制理論我們知道，在第一個與第二個Pole之間曲線，以-20dB/ decade的斜率下降，在第二個Pole 以後則以-40dB/ decade下降。由於-20dB/ decade下降的曲線其橫軸與縱軸數值相乘為一常數，也就是說增益與頻寬相乘為一常數，我們定義他為增益頻寬積(Gain-Bandwidth Product)。如不考慮第二個Pole以後的高頻反應時，運算放大器的頻率增益可以由直流開路增益及增益頻寬積兩個常數即可充分描述。

![開放回路增益圖](./img/5-5png)  
圖5-5 開放回路增益圖

在等效電路中，理想運算放大器輸出後經由功能方塊 $$G(s)$$ 濾波處理完後再輸出，即代表實際的運算放大器的頻率響應。 $$G(s)$$ 除了表現了增益非無限大，也同時表示了輸出阻抗不為零。


### 基本運算放大器放大電路

利用運算放大器可以建構各式的類比訊號處理電路，最基本的為反向放大器，非反向放大器，差動放大器，以及儀表放大器。在分析運算放大器之應用電路時，可使用輸入兩端電壓差永遠為零，流入兩輸入端點電流均為零來分析其放大增益，輸入阻抗以及輸出阻抗。 並於設計時適當調配電阻選用，使 +,- 兩端至地的等效電阻相近。


#### 反向放大器

![反向放大器圖](./img/5-6.png)  
圖5-6 反向放大器圖

由於 $$V_{+-} = 0 \Rightarrow V_- = V_+ = 0 \Rightarrow i_{in} = (v_{in}-0) / R_1 $$  
但 $$ i_- = 0 \Rightarrow i_2 = i_{in} - i_- = (v_{in} -0)/ R_1  $$  
所以 $$ V_{out} = V_- - i_2 R_2 = - V_{in} \frac{R_2}{R_1} $$  
反向放大器的增益為 $$ A_{inv} = \frac{ v_{out} }{ v_{in} } = - \frac{R_2}{R_1}$$。  
由於反向放大器在輸入端加$$V_{in}$$時會造成輸入電流為 $$v_{in}/R_1$$ 因此 反向放大器的輸入阻抗為 $$ R_1 $$ 。  
至於輸出端，由於在未線性工作範內，電壓完全由 $$v_{in} A_{inv}$$ 所決定，不受負載阻抗影響，所以工作範內輸出阻抗為零。


#### 非反向放大器

![非反向放大器圖](./img/5-7.png)  
圖5-7 非反向放大器圖

由於 $$ v_{+-}=0 , v_-=v_+=v_{in} \Rightarrow i_1= \frac{ (0-v_{in}) }{R_1} $$  
但 $$ i_- = 0 \Rightarrow i_2=i_1=- \frac{v_{in} }{R_1} $$  
$$ \Rightarrow v_{in} - v_{out} = R_2 i_2 = -v_{in} \frac{R_2}{R_1} $$  
$$ \Rightarrow v_{out} = ( 1 + \frac{R_2}{R_1} ) v_{in} $$  
非反向放大的增益為 $$A_{min}=1+R_2/R_1$$
反向放大器由於輸入端不容任何電流流入，輸入阻抗為無窮大。
輸出阻抗因為直接由運算放大器之輸出而來，輸出阻抗為零。

#### 差動放大器

多數的感測器感測訊號很微小，而且當它被以訊號線傳送到後方訊號處理電路時，難免會受到電磁波干擾而混入雜訊。訊號與雜訊的能量比例為訊雜比，當訊雜比小時，需要有一個可以壓抑雜訊放大訊號的電路才能解除電磁波干擾雜訊的困擾。  

幸好對於兩條線同軸線或相絞成雙絞線時，這兩條線所接收到的電磁波干擾雜訊，會是相同的，即同大小同相位，專有名詞稱這種訊號為共模訊號(Common Mode)。相對於共模訊號，另一種同大小，但相位相差180度的訊號，專有名詞稱為差動訊號(Differential Mode)。任何兩個訊號$$x_1 , x_2$$我們都可以將它分解成這兩個訊號的共模訊號分量， $$ x_{cm} = \frac{x_1+x_2}{2} $$ 以及兩訊號的差動訊號分量 $$ x_{diff} = \frac{x_1-x_2}{2} $$ ，即 $$x_1=x_{cm} + x_{diff}$$ ， $$x_1=x_{cm} - x_{diff}$$ 。由於雙絞線或同軸線的電磁波干擾雜訊本身為共模訊號。若感測器測量訊號為一對訊號如$$x_1 , x_2$$，而內涵與感測物理量成正比的為這對訊號的差值，即 $$x_1-x_2=x_{diff}$$ ，則這兩訊號經由雙絞線或同軸線這到後端處理時被滲入了很大的共模訊號，即 $$s_1=x_1+s_n$$ ， $$s_2=x_2+s_n$$ 。當把含有共模訊號的兩訊號 $$s_1,s_2$$ 饋入能夠執行差動訊號放大的放大電路之後，其差動成份 $$s_{diff} = \frac{s_1-s_2}{2} = \frac{x_1-x_2+s_n-s_n}{2} = x_{diff} $$ 即為原訊號的差動成份，會被放大很高的差動放大倍率。而其共模部份 $$ s_{cm} = \frac{s_1+s_2}{2} = x_{cm} + s_n $$ 則會以遠低於差動放大倍率，壓抑而縮小。    
$$\frac{ \text{差動放大率} }{ \text{共模放大率} } = \text{共模免疫比} $$ Common mode rejection ratio  CMRR。  

圖5-8 為差動放大器電路，它是反向放大及非反向放大器的綜合體，由電路分析可以得知其輸出為 $$v_{out} = (v_- - v_+) \frac{R_2}{R_1} $$ 也就是差動增益為 $$R_2/R_1$$ 可以任意調整，而+極輸入阻抗為 $$R_2+R_1$$ ，-極亦非極大，輸出阻抗極小。  

![差動放大器](./img/5-8.png)  
圖5-8 差動放大器

一個電路如果所傳送的訊號是電壓型式時，電路的每一級均可以視為對前一級的負載電抗稱輸入阻抗而對下一級則是電壓控制電壓源串聯輸出阻抗。理想的電路是每一級均有無限大的輸入阻抗，零輸出阻抗，可以任意調整的電壓控制電壓源增益。  

除了阻抗匹配考量之外，為了能有更大的抗干擾能力，電路必須能夠執行差動放大。綜合起來理想的電路應有以下的特點：
  - 高輸入阻抗。
  - 低輸出阻抗。
  - 能任意調增益。
  - 具有差動放大能力。


檢視前面所討論過的各式大放大電路可以發現，均僅能滿足部份條件：  
  - 單純運算放大器： 高輸入阻抗，低輸出阻抗及差動輸入均能滿足但無法任意調增益。
  - 反向放大器： 低輸出阻抗且能調增益，但一方面輸入阻抗不高，且不能夠做差動輸入放大。
  - 非反向放大器：高輸入阻抗，低輸出阻抗又能調增益，但不能做差動輸入的放大。
  - 差動放大器：可做差動放大，可調增益，有極小的輸出阻抗但輸入阻抗不見得小。

#### 儀表放大器

圖5-9 儀表放大器(Instrumental Amplifier,IA)利用反向放大器有極大的輸入阻抗而差動放大器能做差動放大，兩者互補，以兩組非反向放大器分別放大+,-極訊號而後再以差動放大器做它們之間的差動放大。由於是經由兩級的放大放的增益即為兩級增益的積。至於輸入阻抗則同非反向放大器一樣為極大。輸出阻抗同差動放大器一樣為極小。


![儀表放大器由兩個非反向放大器與一個差動放大器共同組成](./img/5-9.png)  
圖5-9 儀表放大器由兩個非反向放大器與一個差動放大器共同組成

要達成差動放大器差動放大增益極大化而共模增益極小化的目標，有賴於差動放大週邊電阻值的準確配對，一般自製電路要選出電阻準確性有限。儀表放大器內的電阻配對，係以半導體製程製作完成之後，再用雷射光一邊實測一邊整修，比較容易達成高準確度。

圖5-10  AD620即是ADI公司所製造的現成儀表放大器。左邊為它的接腳圖，右為當測試AD620動態特性settling time時所用的電路。在使用時，差動訊號由2,3腳接入，1,8腳之間的電 $$ R_G $$ 阻，可以用來設定增益大小。$$G = \frac{49.9 k \Omega}{G-1}  $$7,4分別接正負電源，5接到地，6接輸出。


![AD620儀表放大器](./img/5-10.png)  
圖5-10 AD620儀表放大器。
