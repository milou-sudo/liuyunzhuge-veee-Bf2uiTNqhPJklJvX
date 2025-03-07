
最近发现现在租房还是签合同，越来越多采用电子签约的方式进行，好处不用多说节约成本，节约时间。抱着好奇的心理，尝试自己动手实现一个电子签。原来并不复杂主要通过了canvas绘画能力进行实现的。
**主要功能**


1. 创建合同模板：使用HTML和CSS创建一个合同模板。
2. 添加签名区域：在合同模板中添加一个区域用于签名。
3. 实现签名功能：使用元素实现签名功能。
4. 将签名保存到合同模板：将签名绘制到合同模板中。
5. 提供下载功能：允许用户下载带有签名的合同png/pdf格式。
**展示效果如下**
![](https://img2024.cnblogs.com/blog/1731300/202501/1731300-20250108182544611-2062694176.png)


![](https://img2024.cnblogs.com/blog/1731300/202501/1731300-20250108182612776-1166584056.png)


### 实现


主要三个重要点：实现签名，签名放到指定位置，保存pdf/png


#### 1\. 实现签名



```
const canvas = $('#signatureCanvas')[0]// document.getElementById('signatureCanvas');
    const ctx = canvas.getContext('2d');
    let drawing = false;

    $('#signatureCanvas').on('mousedown touchstart', (e) => {
        drawing = true;
        draw(e);
    });

    $('#signatureCanvas').on('mousemove touchmove', (e) => {
        if (drawing) {
            draw(e);
        }
    });

    $('#signatureCanvas').on('mouseup mouseout touchend', () => {
        drawing = false;
        ctx.beginPath();
    });
    function draw(e) {
        const rect = canvas.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        ctx.lineWidth = 4;
        ctx.lineCap = 'round';
        ctx.strokeStyle = '#000000';

        ctx.lineTo(x, y);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(x, y);
    }

```

#### 2\. 签名放到指定位置



```
//取消签名
    document.getElementById('clearButton').addEventListener('click', () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    });
    // 保存签名
    document.getElementById('saveButton').addEventListener('click', () => {
        const dataURL = canvas.toDataURL();
        let name = '.' + signName
        if (name) {
            const signTexts = document.querySelectorAll(name);
            signTexts.forEach(signText => {
                signText.style.backgroundImage = `url(${dataURL})`;
                signText.style.backgroundSize = 'cover';
                signText.textContent = ''; // 清空文本内容
            });
            $('.pop').hide();
        }
    });

```

#### 3\. 保存pdf/png


这里用到了html2canvas保存图片格式，html2pdf保存pdf格式



```
  // 下载签名与输入完成后的后合同
    $('#downloadAgree').click(function () {
        // 你可以指定要转换为PDF的元素
        const element = document.body;
        /*
        html2pdf()
            .from(element)
            .save('page.pdf');
        */

        // 将当前页面保存为png格式
        html2canvas(element).then(canvas => {
            // 创建一个a标签来下载PNG文件
            const link = document.createElement('a');
            link.href = canvas.toDataURL('image/png');
            link.download = 'page.png';
            element.appendChild(link);
            link.click();
            element.removeChild(link);
        });


    });

```

### 完成代码



```
html>
<html lang="en">

<head>
     <meta name="author" content="cypking">meta>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Documenttitle>
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js">script>

    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js">script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js">script>
head>
<style>
    html,
    body {
        height: 100%;
        width: 100%;
        padding: 20px;
    }

    .pop {
        height: 100vh;
        width: 100vw;
        position: fixed;
        top: 0;
        left: 0;
        background: rgba(0, 0, 0, .5);
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .mark {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }

    h1 {
        text-align: center;
        margin: auto;
    }

    .sign {
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: space-around;
    }

    input {
        display: inline-block;
        margin-left: 10px;
        margin-top: 10px;

        height: 24px;
        line-height: 24px;

    }

    dl {
        display: flex;
        flex-direction: column;
        align-items: flex-end;

    }

    dt,
    dd {
        display: flex;
        align-items: center;
    }

    .sign-text,
    .sign-date,
    .sign-text1,
    .sign-date1 {
        margin-top: 10px;
        height: 40px;
        width: 80px;
        border: 1px dashed #ccc;
        display: flex;
        align-items: center;
        text-align: center;
        justify-content: center;
        border-radius: 4px;
        cursor: pointer;
    }

    #signatureCanvas {
        background-color: white;
    }

    #downloadAgree {
        display: inline-block;
        margin: 20px auto 40px;
    }

    .controlPanel {
        position: fixed;
        bottom: 20px;
        right: 20px;
    }

    .party {
        width: 50px;
        height: 50px;
        border-radius: 100%;
        border: 1px dashed black;
        position: relative;
        margin-bottom: 20px;
    }

    .partya::after {
        content: '甲';
        width: 16px;
        height: 16px;
        border-radius: 100%;
        font-size: 12px;
        line-height: 16px;
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto;
    }

    .partyb::after {
        content: '乙';
        width: 16px;
        height: 16px;
        border-radius: 100%;
        font-size: 12px;
        line-height: 16px;
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto;
    }
style>

<body>
    <span>✏️span>
    
    <div class="agreement">
        <h1> 居住房屋租赁合同h1>
        <br />
        <br>出租人 (甲方): <input type="text" placeholder="请输入" />
        <br>证件类型及编号:<input type="text" placeholder="请输入" />
        <br>
        <br>承租人 (乙方):<input type="text" placeholder="请输入" />
        <br>证件类型及编号:<input type="text" placeholder="请输入" />
        <br>
        <br>根据《中华人民共和国民法典》等有关法律、法规的规定，甲、乙双方在平等、自愿、公平和诚实信用的基础上，经协商一致，就乙方承租甲方可依法出租的房屋事宜，订立本合同。
        <br>
        <br>租金及支付方式
        <br>租赁起止日期: 2025-02-01 至 2026-02-01
        <br>租赁期限:1年
        <br>
        <br>方式①
        <br>租金付款方式:年付****12元人民币
        <br>押金：***元人民币
        <br>总金额：***元人民币
        <br>（大写）：***
        <br>期数：1次
        <br>
        <br>租客身份信息
        <br>姓名:<input type="text" placeholder="请输入" />
        <br>性别:<input type="text" placeholder="请输入" />
        <br>联系电话:<input type="text" placeholder="请输入" />
        <br>证件类型:<input type="text" placeholder="请输入" />
        <br>证件号码:<input type="text" placeholder="请输入" />
        <br>工作：<input type="text" placeholder="请输入" />
        <br>
        <br>条款内容
        <br>第一条出租房屋情况和租赁用途
        <br>1-1.甲方将*** 房屋出租给乙方。该房屋建筑面积为 *** 平方米。
        <br>1-2.乙方向甲方承诺，承租该房屋仅用作居住使用。
        <br>
        <br>第二条交付日期和租赁期限
        <br>2-1.甲、乙双方约定，该房屋租赁期起止时间 2025-02-01 至 2026-02-01
        <br>2-2.双方同意，甲方于 *** 前将该房屋交付给乙方，由乙方进行验收。
        <br>
        <br>第三条 租金及支付方式
        <br>3-1.乙方使用 年付，即在租赁期限内一次性付清支付房租。
        <br>3-2.甲乙双方约定，在租赁期间内，该房屋采用方式①进行支付，房屋押金为 ***元，月租金 *** 元，一次性缴纳费用*** 元，。(注:
        双方约定的租金为甲方净得租金)最后一期租期不足期的，以实际居住天数计算该期租金。在租赁期间内，未经双方协商一致，任何一方不得擅自调整租金标准。
        <br>3-3.该房屋租赁期满后，甲乙双方协商一致续租的，乙方须支付甲方一个月租金作为甲方服务费，则户屋押金自动转为该房屋续租押金，不足部分(若有)由乙方补足。租赁期满后甲、乙双方确认不续租或本合同解除后，房屋押金除用以抵扣应由乙方承担但尚未交纳的费用(包括但不限于租金、违约金等本合同约定由乙方承担的费用)
        外，剩余部分 (若有) 应无息退还乙方。
        <br>
        <br>第四条其他相关费用
        <br>4-1.租赁期限内，该房屋所发生的:水费、电费、煤气费、有线电视费、网络宽带费、物业管理费、室内设施维修费 (人为使用不当的应由乙方承担的除外)、保洁费、暖气费中，甲方承担的费用为:物业管理费、暖气费。
        <br>除甲方承担的费用外，其他费用均由乙方承担；
        <br>4-2.甲方应依法收取本条项下费用(费用标准以国家电网、当地燃气集团、自来水公司等服务提供商制定的为准)。
        <br>
        <br>第五条提供材料
        <br>（一）本人及同住人员，真实有效身份证件正反面照片或纸质版复印件一份
        <br>（二）本人及同住人员联系方式
        <br>（三）宠物种类及数量
        <br>
        <br>第六条 房屋使用要求和维修责任
        <br>5-1.甲方应确保该房屋交付时符合规定的安全条件。乙方入住 30 天内附属设施、设备非乙方使用不当或不合理使用有损坏或故障时，甲方负责维修;乙方入住超过 30
        天后，附属设施、设备有损坏或故障时由乙方自行维修解决，甲方不负责维修。
        <br>5-2.乙方应对该房屋的使用安全负责。租赁期间，乙方应合理使用并爱护该房屋及其附属设施、设备因乙方使用不当或不合理使用，致使该房屋及其附属设施、设备损坏或发生故障的，乙方应负责修复，乙方不维修的，甲方可代为维修，费用由乙方承担。
        <br>5-3.租赁期间，甲方应定期对该房屋进行检查、养护，保证该房屋及其附属设施、设备处于正常的可使用和安全的状态。甲方应在检查养护前 3 日通知乙方。检查养护时，乙方应予以配合。甲方应减少对乙方使用该房屋的影响。
        <br>5-4.租赁期间，乙方须全力配合甲方因该房屋注册公司 (含:工商、税务、资质审批部门)所需要的实地核查，每年实地核查次数不超过 3次。若乙方以各种理由拒不配合视乙方违约，赔偿甲方壹个月房租做为违约金。
        <br>
        <br>第七条 转租、承租权转让和交换
        <br>6-1.租赁期间，乙方无转租权。
        <br>6-2.租赁期间，乙方不得将该房屋转让给他人承租或与他人承租的居住房屋进行交换
        <br>
        <br>第八条 续租
        <br>7-1. 该房屋租赁期限届满、乙方需继续承租的，乙方应于租赁期限届满前三十日内向甲方提出续租，经甲、乙双方协商一致后，重新签订合同。乙方支付续租账单 (包括但不限于支付租金或补足续租押金)
        即视为乙方已完全了解续租租期、续租租金等重要信息并确认该续租合同，与甲方建立房屋租赁关系。乙方不续租的，甲方有权在租赁期限届满前三十日内带人看房，乙方需给予配合。
        <br>
        <br>第九条 房屋返还
        <br>8-1.除甲方同意乙方续租外，乙方应在本合同的租期届满后 1日内返还该房屋
        <br>8-2.乙方未经甲方书面同意逾期返还该房屋的，每逾期一日，乙方应按该房屋日租金的 三倍向甲方支付房屋占用费。
        <br>8-3.除本合同附件另有约定外，乙方返还该房屋时，该房屋及其装修、附属设施和设备应当符合正常使用后的状态。经甲方书面验收认可后，相互结清各自应当承担的费用
        <br>
        <br>第十条 解除本合同的条件
        <br>10-1.甲、乙双方同意在租赁期内，有下列情形之一的，本合同解除，双方互不担责:
        <br>(一) 该房屋占用范围内的土地使用权依法提前收回的;
        <br>(二) 该房屋因公共利益需要被依法征收的;
        <br>(三) 该房屋因城市建设需要被依法列入房屋拆迁许可范围的;
        <br>(四) 该房屋因不可抗力原因毁损、灭失，致使乙方不能正常使用的:
        <br>(五) 签订本合同时，甲方已告知乙方该房屋已设定抵押，租赁期间被处分的;
        <br>(六) 甲乙双方协商一致解除本合同的。
        <br>10-2.甲、乙双方同意，甲方有下列情形之一的，乙方可书面通知甲方解除本合同，并有权要求甲方赔偿损失。
        <br>(一) 甲方未按合同约定按时交付该房屋，经乙方书面催告后 7日内仍未交付的;
        <br>(二) 甲方交付的该房屋不符合本合同约定或存在重大质量缺陷，致使乙方不能正常使用的;
        <br>
        <br>10-3.甲、乙双方同意，乙方有下列情形之一的，甲方可书面通知乙方解除本合同，并有权要求乙方赔偿损失。
        <br>(一) 乙方擅自改变房屋居住用途的;
        <br>(二) 因乙方原因造成房屋结构损坏的:
        <br>(三) 乙方擅自转租该房屋、转让该房屋承租权或与他人交换各自承租的房屋的;
        <br>(四) 乙方擅自增加承租同住人，或人均承租建筑面积、使用面积低于规定标准的
        <br>(五) 乙方利用承租的居住房屋从事违法违规活动的:
        <br>(六) 乙方逾期不支付本合同项下约定由乙方承担的费用的(包括但不限于房屋押金、租金、水电煤费用等);
        <br>(七)乙方擅自将该房屋钥匙交付或配给非居住人员的;
        <br>(八)乙方隐瞒、漏报、谎报自身传染性疾病或隐性疾病的;
        <br>(九) 乙方在未承租的房间堆放私人物品的。
        <br>(十) 欠缴各项费用达月租金额 15%的。
        <br>
        <br>第十一条违约责任
        <br>11-2.甲方未在本合同中告知乙方，该房屋出租前已抵押，造成乙方损失的，甲方应负责赔偿。
        <br>11-3.乙方未征得甲方书面同意或者超出甲方书面同意的范围擅自装修房屋或者增设附属设施的，甲方可以要求乙方恢复房屋原状并赔偿损失。
        <br>11-4.租赁期间，非本合同约定的情况，甲方提前解除合同，甲方应向乙方支付壹个月房屋租金作为违约金。若违约金不足抵付乙方损失的，甲方还应负责赔偿。
        <br>11-5.租赁期间，非本合同约定的情况，乙方中途擅自退租的，乙方应向甲方支付壹个月房屋租金作为违约金。若违约金不足抵付甲方损失的，乙方还应负责赔偿。
        <br>11-6.租赁期间，甲方有本合同第 10-2条约定情形之一的，应向乙方支付贰个月房屋租金作为违约金，若违约金不足抵付乙方损失的，甲方还应负责赔偿。
        <br>11-7.租赁期间，乙方有本合同第 10-3 条约定情形之一的，应向甲方支付贰个月房屋租金作为违约金，若违约金不足抵付甲方损失的，乙方还应负责赔偿。
        <br>11-8租赁期间，乙方承诺租赁期内不使用煤气罐，不给电动车电池充电，若乙方私自违反承诺，造成意外事故与甲方无关，所有责任由乙方自行承担。合同期内甲方不定期实地检查屋内安全隐患，一经发现乙方使用煤气罐或给电动车电池充电行为，视乙方严重违约，甲方有权收回房屋且押金不退。
        <br>
        <br>第十二条 其他条款
        <br>12.1
        租赁期间内，承租人是房屋的实际管理人，承租人需时刻注意防火、防盗、防触电，不做危及自身人身安全的活动，房屋内发生的一切安全事故都由承租人来承担，与出租人无关，包括但不限于高空抛物，水电煤气使用不当，在房屋内摔倒等，如果承租人利用此房进行不正当的经营或者违法活动，出租方有权无条件的立刻收回房屋，给出租方造成损失的，要按照实际损失进行赔偿。
        <br>12.2.本合同未尽事宜，经甲、乙双方协商一致，可订立补充条款。本合同补充条款及附件，为本合同不可分割的一部分。
        <br>12.3.本合同为电子合同，经乙方在本系统点击”确认签署“或支付本合同项下账单 (该房屋租金及/或押金账单) 后生效。
        <br>12.4.本合同生效后，甲、乙双方对合同内容的变更或补充应另行订立合同，作为本合同的附件。本合同附件与本合同具有同等法律效力。
        <br>
        <br>第十三条 解决争议的方式
        <br>11-1.本合同由中华人民共和国法律、法规管辖。
        <br>11-2.双方在履行本合同过程中若发生争议的，甲、乙双方可协商解决或者向人民调解委员会申请调解，也可选择下列第二种方式解决:
        <br>(一) 提交当地仲裁委员会仲裁，(二) 依法向人民法院提起诉讼。
        <br>
        <br>其它内容
        <br>房间家具清单:
        <br>床2。床垫2。衣柜1。茶几1。沙发1。电视柜 1。椅子4。窗帘3。冰箱1。洗衣机1。钥匙 1。电卡 1。户门禁 1。油烟机 1。洗碗机 1。茶几1。餐桌1。
        <br>公共家具清单:
        <br>
        <br>
        <br>
        <br>
        <br>
        <div class="sign">
            <dl>
                <dt>甲方（出租人）签字：<div class="sign-text">签字div>
                dt>
                <dd>日期：<div class="sign-date">签字div>
                dd>
            dl>
            <dl>
                <dt>乙方（承租人）签字：<div class="sign-text1">签字div>
                dt>
                <dd>日期：<div class="sign-date1">签字div>
                dd>
            dl>
        div>
    div>
    <div class="controlPanel">
        <div class="party partya">div>
        <div class="party partyb">div>
        <button id="downloadAgree">下载合同button>
    div>

    <div class="pop">
        <div class="mark">
            <canvas id="signatureCanvas" width="700" height="400" style="border:1px solid #000000;">canvas>
            <div>
                <button id="clearButton">清除签名button>
                <button id="saveButton">保存签名button>
            div>

        div>
    div>
body>
<script>
    $('.pop').hide();
    let signName = ''
    $(' .sign-text,.sign-date ,  .sign-text1,.sign-date1').click(function (e) {
        console.log(111, e.target.className);
        signName = e.target.className
        $('.pop').show();
    });
    const canvas = $('#signatureCanvas')[0]// document.getElementById('signatureCanvas');
    const ctx = canvas.getContext('2d');
    let drawing = false;

    $('#signatureCanvas').on('mousedown touchstart', (e) => {
        drawing = true;
        draw(e);
    });

    $('#signatureCanvas').on('mousemove touchmove', (e) => {
        if (drawing) {
            draw(e);
        }
    });

    $('#signatureCanvas').on('mouseup mouseout touchend', () => {
        drawing = false;
        ctx.beginPath();
    });
    function draw(e) {
        const rect = canvas.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        ctx.lineWidth = 4;
        ctx.lineCap = 'round';
        ctx.strokeStyle = '#000000';

        ctx.lineTo(x, y);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(x, y);
    }
    //取消签名
    document.getElementById('clearButton').addEventListener('click', () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    });
    // 保存签名
    document.getElementById('saveButton').addEventListener('click', () => {
        const dataURL = canvas.toDataURL();
        let name = '.' + signName
        if (name) {
            const signTexts = document.querySelectorAll(name);
            signTexts.forEach(signText => {
                signText.style.backgroundImage = `url(${dataURL})`;
                signText.style.backgroundSize = 'cover';
                signText.textContent = ''; // 清空文本内容
            });
            $('.pop').hide();
        }
    });
script>
<script>
    // 下载签名与输入完成后的后合同
    $('#downloadAgree').click(function () {
        // 你可以指定要转换为PDF的元素
        const element = document.body;
        /*
        html2pdf()
            .from(element)
            .save('page.pdf');
        */

        // 将当前页面保存为png格式
        html2canvas(element).then(canvas => {
            // 创建一个a标签来下载PNG文件
            const link = document.createElement('a');
            link.href = canvas.toDataURL('image/png');
            link.download = 'page.png';
            element.appendChild(link);
            link.click();
            element.removeChild(link);
        });


    });
script>

html>

```

 本博客参考[樱花宇宙官网](https://yzygzn.com)。转载请注明出处！
