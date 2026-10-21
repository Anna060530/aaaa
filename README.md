<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <title>數位名片產生器</title>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&display=swap" rel="stylesheet">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <style>
    :root{
      --acc:#5b4bff;
      --p:#fff;
      --t:#161d2f;
      --m:#667085;
      --f:#f2f4f8;
      --l:#e3e7ee;
      box-sizing:border-box;
      padding-top:env(safe-area-inset-top,0px);
      padding-bottom:env(safe-area-inset-bottom,0px);
    }
    @media (prefers-color-scheme:dark){
      :root:not([data-theme="light"]){
        --p:#171e30;--t:#eef1f8;--m:#9aa5ba;--f:#212a40;--l:#2c3650;
      }
    }
    :root[data-theme="dark"]{--p:#171e30;--t:#eef1f8;--m:#9aa5ba;--f:#212a40;--l:#2c3650}
    html{scroll-padding-top:env(safe-area-inset-top,0px);background:#0c1120}
    *{box-sizing:border-box}
    body{margin:0;min-height:100vh;color:var(--t);font:15px/1.6 "Noto Sans TC","PingFang TC","Microsoft JhengHei",sans-serif;background:radial-gradient(700px 600px at 72% 40%,color-mix(in srgb,var(--acc) 38%,transparent),transparent 70%),#0c1120}
    button,input,textarea{font:inherit;color:inherit}
    :focus-visible{outline:3px solid var(--acc);outline-offset:2px}
    .app{display:grid;grid-template-columns:minmax(0,480px) 1fr;gap:40px;max-width:1100px;margin:0 auto;padding:28px 24px;min-height:100vh}
    .ed{background:var(--p);border-radius:28px;padding:24px;align-self:start;box-shadow:0 30px 60px -30px #000}
    .brand{display:flex;align-items:center;gap:12px;margin-bottom:18px}
    .mk{width:42px;height:42px;border-radius:13px;display:grid;place-items:center;font-size:21px;background:var(--acc);color:#fff}
    .brand b{display:block;font-size:18px;font-weight:900;line-height:1.2}.brand span{font-size:13px;color:var(--m)}
    .steps{display:grid;grid-template-columns:repeat(5,1fr);gap:6px;background:var(--f);padding:5px;border-radius:16px;margin-bottom:22px}
    .steps button{border:0;background:none;border-radius:12px;padding:9px 4px;font-weight:700;font-size:14px;color:var(--m);cursor:pointer;transition:background .15s,color .15s}
    .steps button[aria-selected=true]{background:var(--p);color:var(--acc);box-shadow:0 2px 8px -2px rgba(20,30,60,.25)}
    .sec{display:none}.sec.on{display:block}
    .sec h2{font-size:22px;margin:0 0 4px;font-weight:900}
    .sec .sub{color:var(--m);margin:0 0 18px;font-size:14px}
    .sec h3{font-size:15px;margin:24px 0 8px;font-weight:700}
    .g{display:grid;grid-template-columns:1fr 1fr;gap:12px}.fu{grid-column:1/-1}
    label.f{display:block;font-size:13px;font-weight:500;color:var(--m)}
    .in{display:block;width:100%;margin-top:5px;min-height:44px;border:1.5px solid transparent;background:var(--f);border-radius:12px;padding:10px 13px;color:var(--t);transition:border-color .15s,box-shadow .15s,background .15s}
    .in:focus{outline:0;background:var(--p);border-color:var(--acc);box-shadow:0 0 0 4px color-mix(in srgb,var(--acc) 18%,transparent)}
    textarea.in{resize:vertical;min-height:76px}
    .up{display:flex;align-items:center;gap:14px;padding:12px;border:1.5px dashed var(--l);border-radius:16px}
    .th{width:60px;height:60px;border-radius:50%;background:var(--f) center/cover no-repeat;flex:none}.th.sq{border-radius:14px;background-size:contain}
    .btn{border:1.5px solid var(--l);background:var(--p);border-radius:12px;padding:9px 16px;cursor:pointer;font-weight:700;transition:transform .12s,background .15s}
    .btn:hover{background:var(--f)}.btn:active{transform:scale(.97)}
    .btn.p{background:var(--acc);border-color:var(--acc);color:#fff}.btn.p:hover{filter:brightness(1.1)}
    .btn.lg{width:100%;padding:14px;font-size:16px;border-radius:14px}
    .btn.add{width:100%;border-style:dashed;color:var(--acc);background:none;border-color:color-mix(in srgb,var(--acc) 50%,var(--l))}
    .row{display:grid;grid-template-columns:48px 1fr 1.3fr 34px 34px;gap:6px;align-items:center;margin-bottom:8px}
    .row .in{margin:0}
    .ico{width:48px;height:48px;border-radius:13px;border:1.5px dashed color-mix(in srgb,var(--acc) 45%,var(--l));background:var(--f);display:grid;place-items:center;font-size:21px;cursor:pointer;overflow:hidden;padding:0}
    .ico img,.pi img,.soc img{width:100%;height:100%;object-fit:cover}
    .x{border:0;background:none;color:var(--m);font-size:22px;cursor:pointer;border-radius:8px;height:34px}.x:hover{color:#e5384f}
    .nav{display:flex;gap:10px;margin-top:28px}.nav .btn{flex:1}
    .sw{display:flex;gap:10px;flex-wrap:wrap}
    .sw button{width:38px;height:38px;border-radius:50%;border:3px solid var(--p);box-shadow:0 0 0 1.5px var(--l);cursor:pointer}
    .sw button[aria-pressed=true]{box-shadow:0 0 0 3px var(--t)}
    .guide{margin:0;padding:0;list-style:none;display:grid;gap:10px;counter-reset:s}
    .guide li{counter-increment:s;display:grid;grid-template-columns:28px 1fr;gap:10px;font-size:14px}
    .guide li::before{content:counter(s);width:28px;height:28px;border-radius:50%;background:var(--acc);color:#fff;font-weight:700;display:grid;place-items:center;font-size:13px}
    .guide a{color:var(--acc);font-weight:700}
    .pick{position:absolute;z-index:30;background:var(--p);border:1px solid var(--l);border-radius:16px;padding:8px;display:grid;grid-template-columns:repeat(6,1fr);gap:4px;box-shadow:0 14px 34px rgba(0,0,0,.3);width:290px}
    .pick button{border:0;background:none;font-size:20px;border-radius:10px;cursor:pointer;height:42px;color:var(--t)}.pick button:hover{background:var(--f)}
    .pick .upl{grid-column:1/-1;font-size:13px;height:auto;padding:9px;border:1px solid var(--l);font-weight:700}
    .stage{position:sticky;top:28px;align-self:start;display:flex;justify-content:center}
    .stage .phone{max-width:372px;border:10px solid #05080f;border-radius:48px;box-shadow:0 0 0 2px #2a3350,0 40px 80px -20px #000;position:relative}
    .stage .phone::before{content:"";position:absolute;top:8px;left:50%;transform:translateX(-50%);width:84px;height:22px;border-radius:12px;background:#05080f;z-index:3}
    .fab{display:none}
    .blk{display:grid;grid-template-columns:auto 1fr 34px 34px;gap:8px;align-items:center;padding:8px 12px;border:1.5px solid var(--l);border-radius:14px;margin-bottom:8px}
    .blk.off .bn{opacity:.45;text-decoration:line-through}.blk .x:disabled{opacity:.25;cursor:default}
    .sw2{position:relative;width:42px;height:24px;display:block}.sw2 input{position:absolute;inset:0;opacity:0;cursor:pointer;margin:0;z-index:1}
    .tg{position:absolute;inset:0;border-radius:99px;background:var(--l);transition:background .15s}
    .tg::after{content:"";position:absolute;top:3px;left:3px;width:18px;height:18px;border-radius:50%;background:#fff;transition:transform .15s}
    .sw2 input:checked+.tg{background:var(--acc)}.sw2 input:checked+.tg::after{transform:translateX(18px)}
    .sw2 input:focus-visible+.tg{outline:3px solid var(--acc);outline-offset:2px}
    .chips{display:flex;flex-wrap:wrap;gap:8px}
    .chip{border:1.5px solid var(--l);background:var(--p);border-radius:999px;padding:7px 14px;cursor:pointer;font-weight:500;color:var(--t)}
    .chip[aria-pressed=true]{background:var(--acc);border-color:var(--acc);color:#fff}
    .row.off .in,.row.off .ico{opacity:.4}.eye{font-size:17px}.row.off .eye{opacity:.35}
    .modal{position:fixed;inset:0;background:rgba(5,8,15,.75);backdrop-filter:blur(6px);display:none;place-items:center;z-index:50;padding:20px}.modal.on{display:grid}
    .modal>div{background:#fff;color:#161d2f;border-radius:24px;padding:26px;text-align:center}
    .toast{position:fixed;left:50%;bottom:calc(24px + env(safe-area-inset-bottom,0px));transform:translateX(-50%);background:#161d2f;color:#fff;padding:11px 20px;border-radius:999px;font-size:14px;opacity:0;pointer-events:none;transition:opacity .2s;z-index:60;max-width:90vw;box-shadow:0 10px 30px rgba(0,0,0,.4)}.toast.on{opacity:1}
    @media (max-width:880px){
      .app{grid-template-columns:1fr;padding:14px 12px 90px;gap:0}.ed{padding:18px;border-radius:22px}
      .stage{position:static}.app[data-view=edit] .stage,.app[data-view=view] .ed{display:none}
      .app[data-view=view]{padding-top:24px}.stage .phone{margin:0 auto}
      .fab{display:block;position:fixed;right:16px;bottom:calc(18px + env(safe-area-inset-bottom,0px));z-index:40;border:0;border-radius:999px;background:var(--acc);color:#fff;font-weight:700;padding:13px 22px;box-shadow:0 12px 28px -8px #000}
      .row{grid-template-columns:48px 1fr 34px 34px}.row .u{grid-column:2/5;grid-row:2}}
    @media (prefers-reduced-motion:reduce){*{transition:none!important}}
  </style>

  <style id="cardcss">
    .phone{width:100%;max-width:430px;margin:0 auto;border-radius:30px;background:#fff;overflow:hidden;color:#161d2f;font-family:"Noto Sans TC","PingFang TC","Microsoft JhengHei",sans-serif;line-height:1.6;--c:#5b4bff}
    .phone *{box-sizing:border-box}
    .top{padding:50px 20px 78px;text-align:center;color:#fff;min-height:120px;background:radial-gradient(circle at 88% 12%,rgba(255,255,255,.24) 0 56px,transparent 57px),radial-gradient(circle at 6% 92%,rgba(255,255,255,.14) 0 84px,transparent 85px),linear-gradient(160deg,var(--c),color-mix(in srgb,var(--c) 55%,#000))}
    .logo{background:#fff;padding:6px 12px;border-radius:10px;height:42px;max-width:180px;object-fit:contain;display:block;margin:0 auto 8px;box-shadow:0 6px 14px -6px rgba(0,0,0,.35)}
    .co{font-weight:700;font-size:15px;letter-spacing:.06em}
    .body{padding:0 20px 28px;margin-top:-58px;text-align:center}
    .photo{width:112px;height:112px;border-radius:50%;border:5px solid #fff;background:#e3e7ee center/cover;margin:0 auto;display:grid;place-items:center;font-size:40px;font-weight:900;color:#667085;box-shadow:0 12px 26px -8px rgba(0,0,0,.35)}
    .nm{font-size:26px;font-weight:900;margin:12px 0 0;line-height:1.25}
    .en{color:#667085;font-size:14px;margin:0}
    .tt{font-weight:700;margin:6px 0 0;color:var(--c)}
    .bio{margin:10px 0 0;color:#394559;font-size:14.5px}
    .tags{display:flex;gap:6px;justify-content:center;flex-wrap:wrap;margin-top:10px}
    .tags span{background:color-mix(in srgb,var(--c) 11%,#fff);color:color-mix(in srgb,var(--c) 80%,#000);border-radius:999px;padding:2px 11px;font-size:12.5px;font-weight:500}
    .pbtn{display:flex;align-items:center;justify-content:center;width:100%;border:0;border-radius:16px;padding:15px;font-weight:700;font-size:16px;cursor:pointer;text-decoration:none;margin-top:18px;color:#fff;background:linear-gradient(145deg,var(--c),color-mix(in srgb,var(--c) 68%,#000));box-shadow:0 12px 22px -10px var(--c)}
    .pbtn.alt{background:#fff;color:var(--c);box-shadow:inset 0 0 0 2px var(--c);margin-top:10px}
    .tiles{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:16px}
    .tile{display:flex;align-items:center;gap:9px;padding:11px 12px;border-radius:16px;background:#f3f5f9;color:#161d2f;text-decoration:none;font-size:14px;font-weight:500;text-align:left;min-width:0;transition:transform .15s,box-shadow .15s}
    .tile:hover{transform:translateY(-2px);box-shadow:0 10px 20px -12px rgba(20,32,43,.4)}
    .tile span:last-child{overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
    .pi{width:32px;height:32px;border-radius:11px;background:#fff;display:grid;place-items:center;font-size:17px;flex:none;overflow:hidden;box-shadow:0 2px 6px -2px rgba(20,32,43,.25)}
    .list{display:grid;gap:8px;margin-top:10px}
    .list .tile{border:1px solid #e3e8ee;background:#fff;padding:12px 14px}
    .list .tile::after{content:"\203A";margin-left:auto;font-size:20px;color:#9aa7b3}
    .soc{display:flex;gap:12px;justify-content:center;flex-wrap:wrap;margin-top:20px}
    .soc a{width:48px;height:48px;border-radius:50%;background:#f3f5f9;display:grid;place-items:center;font-size:21px;text-decoration:none;overflow:hidden;transition:transform .15s}
    .soc a:hover{transform:translateY(-2px)}
    .bd{font-style:normal;font-weight:900;font-size:13px;letter-spacing:-.02em;color:var(--c,var(--acc))}
    .addr{margin-top:18px;padding:12px;border-radius:14px;background:#f3f5f9;font-size:13.5px;color:#667085}
    .addr a{color:var(--c);font-weight:700;text-decoration:none}
    .qr{margin:20px auto 0;width:max-content;padding:12px;background:#fff;border:1.5px solid #e3e8ee;border-radius:18px}
    .qr small{display:block;color:#667085;font-size:12px;margin-top:4px}
    .top.np{padding:36px 20px 34px;min-height:0}.body.np{margin-top:0;padding-top:22px}
  </style>
</head>
<body>
  <div class="app" id="app" data-view="edit">
    <main class="ed">
      <div class="brand"><div class="mk">🪪</div><div><b>數位名片產生器</b><span>填好資料，右邊即時預覽</span></div></div>
      <div class="steps" role="tablist" id="steps"></div>

      <section class="sec" data-s="0">
        <h2>基本資料</h2><p class="sub">對方第一眼會看到的內容。</p>
        <div class="g">
          <label class="f">全名<input class="in" data-k="name"></label>
          <label class="f">英文名 / 別名<input class="in" data-k="en"></label>
          <label class="f">頭銜 / 職稱<input class="in" data-k="title"></label>
          <label class="f">公司 / 品牌名稱<input class="in" data-k="company"></label>
          <div class="up fu"><div class="th" id="th-photo"></div><div><b>形象照</b><br><button class="btn" data-up="photo">上傳</button> <button class="btn" data-clr="photo">移除</button></div></div>
          <div class="up fu"><div class="th sq" id="th-logo"></div><div><b>公司 Logo</b><br><button class="btn" data-up="logo">上傳</button> <button class="btn" data-clr="logo">移除</button></div></div>
          <label class="f fu">一句話簡介<textarea class="in" data-k="bio" maxlength="80" placeholder="用 1～2 句話說明你能提供的價值"></textarea></label>
          <label class="f fu">亮點標籤（用逗號分隔）<input class="in" data-k="tags" placeholder="品牌策略, 社群經營"></label>
        </div>
      </section>

      <section class="sec" data-s="1">
        <h2>聯絡方式</h2><p class="sub">點一下就能撥號、寫信或開啟通訊軟體。</p>
        <div class="g">
          <label class="f">電話<input class="in" data-k="phone" type="tel" placeholder="0912345678"></label>
          <label class="f">Email<input class="in" data-k="email" type="email" placeholder="name@example.com"></label>
          <label class="f fu">實體地址<input class="in" data-k="address"></label>
          <label class="f fu">Google 地圖連結（留空會依地址自動產生）<input class="in" data-k="map" placeholder="https://maps.app.goo.gl/..."></label>
        </div>
        <h3>LINE、微信等通訊軟體</h3>
        <div id="L-contact"></div><button class="btn add" data-add="contact">＋ 新增通訊軟體</button>
      </section>

      <section class="sec" data-s="2">
        <h2>社群與作品</h2><p class="sub">每一列可選圖示或上傳自己的 icon，再貼上連結。</p>
        <h3>社群平台</h3>
        <div id="L-social"></div><button class="btn add" data-add="social">＋ 新增社群連結</button>
        <h3>作品集、官網、簡報、價目表</h3>
        <div id="L-work"></div><button class="btn add" data-add="work">＋ 新增連結</button>
        <h3>行動呼籲按鈕</h3>
        <div class="g">
          <label class="f">按鈕文字<input class="in" data-k="ctaLabel" placeholder="預約諮詢"></label>
          <label class="f">按鈕連結<input class="in" data-k="ctaUrl" placeholder="https://..."></label>
        </div>
      </section>

      <section class="sec" data-s="3">
        <h2>版面與背景</h2><p class="sub">用開關決定要不要顯示，用 ▲▼ 調整位置，也可以換背景。</p>
        <h3 style="margin-top:0">區塊順序與顯示</h3><div id="blocks"></div>
        <p class="sub" style="margin:-2px 0 0">頭像、姓名與頭銜固定在最上方。</p>
        <h3>小項目（亮起代表顯示）</h3><div class="chips" id="parts"></div>
        <h3>背景圖片</h3>
        <div class="up"><div class="th sq" id="th-bgTop"></div><div><b>頂部橫幅背景</b><br><button class="btn" data-up="bgTop">上傳</button> <button class="btn" data-clr="bgTop">移除</button></div></div>
        <div class="up" style="margin-top:10px"><div class="th sq" id="th-bgCard"></div><div><b>整張名片背景</b><br><button class="btn" data-up="bgCard">上傳</button> <button class="btn" data-clr="bgCard">移除</button></div></div>
      </section>

      <section class="sec" data-s="4">
        <h2>完成並取得網址</h2><p class="sub">選好顏色，下載你的名片網頁，上傳後就會有專屬網址。</p>
        <h3 style="margin-top:0">主題色</h3><div class="sw" id="sw"></div>
        <h3>1. 下載名片網頁</h3>
        <button class="btn p lg" id="dlh">下載我的名片網頁（index.html）</button>
        <h3>2. 上傳到免費空間，取得專屬網址</h3>
        <ol class="guide">
          <li><span>開啟 <a href="https://app.netlify.com/drop" target="_blank" rel="noopener">Netlify Drop</a>，把檔案拖進去，馬上得到網址。想永久保留，請登入帳號並認領網站。也可以用 GitHub Pages 或 Cloudflare Pages。</span></li>
          <li><span>把得到的網址貼在下面，再複製到 NFC。</span></li>
        </ol>
        <label class="f" style="margin-top:12px">你的專屬網址<input class="in" data-k="cardUrl" placeholder="https://你的名片.netlify.app"></label>
        <h3>3. 寫入 NFC 卡</h3>
        <ol class="guide">
          <li><span>手機安裝 <b>NFC Tools</b>（iOS／Android 都有）。</span></li>
          <li><span>選「寫入」→「新增紀錄」→「網址 / URL」，貼上專屬網址。</span></li>
          <li><span>按「寫入」，把 NFC 卡貼近手機背面。完成後用另一支手機碰卡測試。</span></li>
        </ol>
        <div style="display:flex;gap:8px;margin-top:14px;flex-wrap:wrap"><button class="btn" id="cu">複製網址</button><button class="btn" id="dl">下載 .vcf</button><button class="btn" id="cp">複製 vCard</button><button class="btn" id="rs">重設範例</button></div>
      </section>

      <div class="nav"><button class="btn" id="bk">上一步</button><button class="btn p" id="nx">下一步</button></div>
    </main>

    <aside class="stage"><div class="phone" id="card"></div></aside>
  </div>

  <button class="fab" id="fab">預覽名片</button>
  <div class="modal" id="md"><div><div id="bigqr"></div><p style="margin:10px 0 0">請對方用手機相機掃描</p></div></div>
  <div class="toast" id="ts"></div>

  <script>
    const STORAGE_KEY = "digital-card-v1";
    const STEP_NAMES = ["基本", "聯絡", "連結", "版面", "完成"];
    const ICONS = ["t:in","t:IG","t:f","t:Th","t:GH","t:Be","t:Md","t:X","t:YT","t:LINE","t:微信","💬","📞","✉️","🌐","📄","📍","📅","🛒","🎨","💼","🔗"];
    const COLORS = ["#5b4bff","#0d7a5f","#d6246e","#e0661a","#0e7490","#1d2433"];

    const SAMPLE = {
      name: "林雅婷",
      en: "Tina Lin",
      title: "品牌策略總監",
      company: "晨曦設計",
      photo: "",
      logo: "",
      bio: "我協助中小品牌把故事說清楚，讓客人記得你。",
      tags: "品牌策略, 社群經營, 活動企劃",
      phone: "0912345678",
      email: "tina@example.com",
      address: "台北市信義區松仁路 100 號",
      map: "",
      ctaLabel: "預約 30 分鐘諮詢",
      ctaUrl: "https://example.com/book",
      cardUrl: "",
      color: COLORS[0],
      contact: [{ icon: "t:LINE", label: "LINE", url: "https://line.me/ti/p/~yourid" }],
      social: [
        { icon: "t:in", label: "LinkedIn", url: "https://linkedin.com/in/yourname" },
        { icon: "t:IG", label: "Instagram", url: "https://instagram.com/yourname" },
        { icon: "t:f", label: "Facebook", url: "https://facebook.com/yourname" }
      ],
      work: [
        { icon: "🌐", label: "官方網站", url: "https://example.com" },
        { icon: "📄", label: "服務價目表 PDF", url: "https://example.com/pricing.pdf" }
      ]
    };

    const $ = (selector, root = document) => root.querySelector(selector);
    const $$ = (selector, root = document) => Array.from(root.querySelectorAll(selector));

    let state = loadState();
    let currentStep = 0;

    const blockMeta = {
      bio: "簡介與標籤",
      save: "存入通訊錄按鈕",
      cta: "行動呼籲按鈕",
      contact: "聯絡方式",
      work: "作品與服務連結",
      social: "社群圖示",
      address: "地址與導航",
      qr: "QR Code"
    };

    const defaultOrder = ["bio", "save", "cta", "contact", "work", "social", "address", "qr"];
    const partMeta = {
      photo: "形象照",
      logo: "Logo",
      company: "公司名稱",
      en: "英文名",
      title: "頭銜",
      tags: "亮點標籤",
      phone: "電話",
      email: "Email"
    };

    function escapeHtml(value) {
      return String(value ?? "").replace(/[&<>"']/g, (char) => ({
        "&": "&amp;",
        "<": "&lt;",
        ">": "&gt;",
        '"': "&quot;",
        "'": "&#39;"
      })[char]);
    }

    function loadState() {
      try {
        const saved = JSON.parse(localStorage.getItem(STORAGE_KEY) || "null");
        if (saved && typeof saved === "object") {
          const next = structuredClone(SAMPLE);
          Object.assign(next, saved);
          next.contact = Array.isArray(saved.contact) ? saved.contact : SAMPLE.contact;
          next.social = Array.isArray(saved.social) ? saved.social : SAMPLE.social;
          next.work = Array.isArray(saved.work) ? saved.work : SAMPLE.work;
          next.hide = saved.hide || {};
          next.order = Array.isArray(saved.order) ? saved.order : defaultOrder.slice();
          return next;
        }
      } catch (e) {}
      return structuredClone(SAMPLE);
    }

    function saveState() {
      try {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
      } catch (e) {}
    }

    function showToast(message) {
      const toast = $("#ts");
      toast.textContent = message;
      toast.classList.add("on");
      clearTimeout(showToast.timer);
      showToast.timer = setTimeout(() => {
        toast.classList.remove("on");
      }, 2400);
    }

    function href(url) {
      const trimmed = (url || "").trim();
      if (!trimmed || /^(javascript|data|vbscript):/i.test(trimmed)) return "";
      if (/^(https?:|tel:|mailto:|sms:|weixin:|line:)/i.test(trimmed)) return trimmed;
      if (/^[\\w.-]+@[\\w.-]+\\.[A-Za-z]{2,}$/.test(trimmed)) return "mailto:" + trimmed;
      return "https://" + trimmed;
    }

    function iconMarkup(icon) {
      if (!icon) return "🔗";
      if (icon.startsWith("data:")) return `<img alt="" src="${escapeHtml(icon)}">`;
      if (icon.startsWith("t:")) return `<i class="bd">${escapeHtml(icon.slice(2))}</i>`;
      return escapeHtml(icon);
    }

    function goStep(stepIndex) {
      currentStep = Math.max(0, Math.min(STEP_NAMES.length - 1, stepIndex));
      $$(".sec").forEach((sec) => {
        sec.classList.toggle("on", Number(sec.dataset.s) === currentStep);
      });
      $$("#steps button").forEach((button, index) => {
        button.setAttribute("aria-selected", index === currentStep ? "true" : "false");
      });
      $("#bk").style.visibility = currentStep ? "visible" : "hidden";
      $("#nx").style.display = currentStep === STEP_NAMES.length - 1 ? "none" : "";
      window.scrollTo({ top: 0, behavior: "auto" });
    }

    function buildStepButtons() {
      $("#steps").innerHTML = STEP_NAMES.map((label, index) =>
        `<button role="tab" data-st="${index}" aria-selected="${index === currentStep ? "true" : "false"}">${label}</button>`
      ).join("");
    }

    function fillInputs() {
      $$("[data-k]").forEach((el) => {
        el.value = state[el.dataset.k] ?? "";
      });
      $("#th-photo").style.backgroundImage = state.photo ? `url("${state.photo}")` : "";
      $("#th-logo").style.backgroundImage = state.logo ? `url("${state.logo}")` : "";
      $("#th-bgTop").style.backgroundImage = state.bgTop ? `url("${state.bgTop}")` : "";
      $("#th-bgCard").style.backgroundImage = state.bgCard ? `url("${state.bgCard}")` : "";
    }

    function closePicker() {
      $$(".pick").forEach((picker) => picker.remove());
    }

    function pickFile(maxSize, callback) {
      const input = document.createElement("input");
      input.type = "file";
      input.accept = "image/*";
      input.onchange = () => {
        const file = input.files && input.files[0];
        if (!file) return;
        shrinkImage(file, maxSize, callback);
      };
      input.click();
    }

    function shrinkImage(file, maxSize, callback) {
      const reader = new FileReader();
      reader.onload = () => {
        const img = new Image();
        img.onload = () => {
          const scale = Math.min(1, maxSize / Math.max(img.width, img.height));
          const canvas = document.createElement("canvas");
          canvas.width = Math.round(img.width * scale);
          canvas.height = Math.round(img.height * scale);
          const ctx = canvas.getContext("2d");
          ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
          callback(canvas.toDataURL(file.type === "image/png" ? "image/png" : "image/jpeg", 0.85));
        };
        img.onerror = () => showToast("無法讀取這張圖片");
        img.src = reader.result;
      };
      reader.readAsDataURL(file);
    }

    function renderLinkList(type) {
      const box = $("#L-" + type);
      if (!box) return;
      box.innerHTML = "";

      const arr = state[type] || [];
      arr.forEach((item, index) => {
        const row = document.createElement("div");
        row.className = "row" + (item.off ? " off" : "");
        row.innerHTML =
          '<button class="ico" aria-label="選擇圖示">' + iconMarkup(item.icon) + '</button>' +
          '<input class="in" placeholder="名稱" value="' + escapeHtml(item.label) + '">' +
          '<input class="in u" placeholder="連結或帳號網址" value="' + escapeHtml(item.url) + '">' +
          '<button class="x eye" aria-label="顯示或隱藏" title="顯示或隱藏">👁</button>' +
          '<button class="x del" aria-label="刪除">×</button>';

        const inputs = row.querySelectorAll("input");
        inputs[0].addEventListener("input", (event) => {
          item.label = event.target.value;
          saveState();
          renderCard();
        });
        inputs[1].addEventListener("input", (event) => {
          item.url = event.target.value;
          saveState();
          renderCard();
        });

        row.querySelector(".eye").addEventListener("click", () => {
          item.off = !item.off;
          row.classList.toggle("off", !!item.off);
          saveState();
          renderCard();
        });

        row.querySelector(".del").addEventListener("click", () => {
          state[type].splice(index, 1);
          saveState();
          renderLinkList(type);
          renderCard();
        });

        row.querySelector(".ico").addEventListener("click", (event) => {
          event.stopPropagation();
          openIconPicker(row.querySelector(".ico"), item, () => {
            saveState();
            renderLinkList(type);
            renderCard();
          });
        });

        box.appendChild(row);
      });
    }

    function openIconPicker(button, item, done) {
      closePicker();
      const picker = document.createElement("div");
      picker.className = "pick";

      ICONS.forEach((icon) => {
        const btn = document.createElement("button");
        btn.type = "button";
        btn.innerHTML = iconMarkup(icon);
        btn.addEventListener("click", () => {
          item.icon = icon;
          done();
        });
        picker.appendChild(btn);
      });

      const uploadBtn = document.createElement("button");
      uploadBtn.type = "button";
      uploadBtn.className = "upl";
      uploadBtn.textContent = "上傳自己的圖示";
      uploadBtn.addEventListener("click", () => {
        pickFile(96, (dataUrl) => {
          item.icon = dataUrl;
          done();
        });
      });
      picker.appendChild(uploadBtn);

      const rect = button.getBoundingClientRect();
      picker.style.left = Math.max(8, Math.min(rect.left + window.scrollX, window.innerWidth - 300)) + "px";
      picker.style.top = (rect.bottom + window.scrollY + 6) + "px";
      document.body.appendChild(picker);
    }

    function getOrder() {
      const order = Array.isArray(state.order) ? [...state.order] : [];
      defaultOrder.forEach((key) => {
        if (!order.includes(key)) order.push(key);
      });
      return order.filter((key) => key in blockMeta);
    }

    function renderBlocks() {
      const order = getOrder();
      const box = $("#blocks");
      box.innerHTML = "";

      order.forEach((key, index) => {
        const item = document.createElement("div");
        const hidden = !!(state.hide && state.hide[key]);
        item.className = "blk" + (hidden ? " off" : "");
        item.innerHTML =
          '<label class="sw2"><input type="checkbox" aria-label="顯示 ' + blockMeta[key] + '"' + (hidden ? "" : " checked") + '><span class="tg"></span></label>' +
          '<span class="bn">' + blockMeta[key] + '</span>' +
          '<button class="x" aria-label="上移"' + (index ? "" : " disabled") + '>▲</button>' +
          '<button class="x" aria-label="下移"' + (index < order.length - 1 ? "" : " disabled") + '>▼</button>';

        const checkbox = item.querySelector("input");
        checkbox.addEventListener("change", () => {
          state.hide = state.hide || {};
          state.hide[key] = !checkbox.checked;
          saveState();
          renderBlocks();
          renderCard();
        });

        const buttons = item.querySelectorAll(".x");
        buttons[0].addEventListener("click", () => moveBlock(key, -1));
        buttons[1].addEventListener("click", () => moveBlock(key, 1));
        box.appendChild(item);
      });
    }

    function moveBlock(key, direction) {
      const order = getOrder();
      const index = order.indexOf(key);
      const targetIndex = index + direction;
      if (index < 0 || targetIndex < 0 || targetIndex >= order.length) return;
      const next = [...order];
      [next[index], next[targetIndex]] = [next[targetIndex], next[index]];
      state.order = next;
      saveState();
      renderBlocks();
      renderCard();
    }

    function renderParts() {
      const target = $("#parts");
      if (!target) return;
      target.innerHTML = Object.keys(partMeta).map((key) => {
        const hidden = !!(state.hide && state.hide[key]);
        return '<button class="chip" data-part="' + key + '" aria-pressed="' + (!hidden) + '">' + partMeta[key] + '</button>';
      }).join("");
    }

    function isHidden(key) {
      return !!((state.hide && state.hide[key]) || false);
    }

    function getMapLink() {
      if (href(state.map)) return href(state.map);
      if (state.address) return "https://www.google.com/maps/search/?api=1&query=" + encodeURIComponent(state.address);
      return "";
    }

    function buildCardMarkup(standalone = false) {
      const hidden = isHidden;
      const topBg = state.bgTop ? ` style="background-image:linear-gradient(rgba(0,0,0,.28),rgba(0,0,0,.28)),url('${state.bgTop}');background-size:cover;background-position:center"` : "";
      const tags = (state.tags || "").split(/[,，、]/).map(x => x.trim()).filter(Boolean);

      let html = '<div class="top' + (hidden("photo") ? " np" : "") + '"' + topBg + '>';
      if (state.logo && !hidden("logo")) {
        html += '<img class="logo" alt="" src="' + escapeHtml(state.logo) + '">';
      }
      if (state.company && !hidden("company")) {
        html += '<div class="co">' + escapeHtml(state.company) + '</div>';
      }
      html += '</div>';

      html += '<div class="body' + (hidden("photo") ? " np" : "") + '">';

      if (!hidden("photo")) {
        html += '<div class="photo"' + (state.photo ? ' style="background-image:url(\'' + state.photo + '\')"' : "") + '>' + (state.photo ? "" : escapeHtml((state.name || state.en || "?").charAt(0))) + '</div>';
      }

      html += '<h1 class="nm">' + escapeHtml(state.name || "你的姓名") + '</h1>';
      if (state.en && !hidden("en")) html += '<p class="en">' + escapeHtml(state.en) + '</p>';
      if (state.title && !hidden("title")) html += '<p class="tt">' + escapeHtml(state.title) + '</p>';

      const bioBlock = (state.bio ? '<p class="bio">' + escapeHtml(state.bio) + '</p>' : "") +
        (tags.length && !hidden("tags") ? '<div class="tags">' + tags.map(tag => '<span>' + escapeHtml(tag) + '</span>').join("") + '</div>' : "");
      const saveBtn = '<button class="pbtn" id="pv-save">存入手機通訊錄</button>';
      const ctaBtn = (state.ctaLabel && href(state.ctaUrl)) ? '<a class="pbtn alt" target="_blank" rel="noopener" href="' + escapeHtml(href(state.ctaUrl)) + '">' + escapeHtml(state.ctaLabel) + '</a>' : "";

      const contactLinks = [];
      if (state.phone && !hidden("phone")) {
        contactLinks.push(['📞', '撥打電話', 'tel:' + state.phone.replace(/[^\\d+]/g, "")]);
      }
      if (state.email && !hidden("email")) {
        contactLinks.push(['✉️', state.email, 'mailto:' + state.email]);
      }
      (state.contact || []).forEach((item) => {
        if (href(item.url) && !item.off) {
          contactLinks.push([item.icon, item.label || item.url, href(item.url)]);
        }
      });
      const contactBlock = contactLinks.length ? '<div class="tiles">' + contactLinks.map(([icon, label, url]) =>
        '<a class="tile" target="_blank" rel="noopener" href="' + escapeHtml(url) + '"><span class="pi">' + iconMarkup(icon) + '</span><span>' + escapeHtml(label) + '</span></a>'
      ).join("") + '</div>' : "";

      const workItems = (state.work || []).filter(item => href(item.url) && !item.off);
      const workBlock = workItems.length ? '<div class="list">' + workItems.map(item =>
        '<a class="tile" target="_blank" rel="noopener" href="' + escapeHtml(href(item.url)) + '"><span class="pi">' + iconMarkup(item.icon) + '</span><span>' + escapeHtml(item.label || item.url) + '</span></a>'
      ).join("") + '</div>' : "";

      const socialItems = (state.social || []).filter(item => href(item.url) && !item.off);
      const socialBlock = socialItems.length ? '<div class="soc">' + socialItems.map(item =>
        '<a target="_blank" rel="noopener" aria-label="' + escapeHtml(item.label || '社群連結') + '" href="' + escapeHtml(href(item.url)) + '">' + iconMarkup(item.icon) + '</a>'
      ).join("") + '</div>' : "";

      const mapLink = getMapLink();
      const addressBlock = state.address ? '<p class="addr">' + escapeHtml(state.address) + '<br><a target="_blank" rel="noopener" href="' + escapeHtml(mapLink) + '">Google 地圖導航</a></p>' : "";

      const qrBlock = '<div class="qr" id="qr"' + (standalone ? '' : ' role="button" tabindex="0" aria-label="放大 QR Code" style="cursor:pointer"') + '><div id="qrbox"></div><small>' + (standalone ? '掃描即可分享給朋友' : '點擊放大，讓對方掃描') + '</small></div>';

      const sectionMap = {
        bio: bioBlock,
        save: saveBtn,
        cta: ctaBtn,
        contact: contactBlock,
        work: workBlock,
        social: socialBlock,
        address: addressBlock,
        qr: qrBlock
      };

      const order = getOrder();
      order.forEach((key) => {
        if (!hidden(key) && sectionMap[key]) {
          html += sectionMap[key];
        }
      });

      html += '</div>';
      return html;
    }

    function renderCard() {
      const color = state.color || COLORS[0];
      document.documentElement.style.setProperty("--acc", color);
      $$("#sw button").forEach((button) => {
        button.setAttribute("aria-pressed", button.dataset.c === color ? "true" : "false");
      });

      const card = $("#card");
      card.style.setProperty("--c", color);
      card.innerHTML = buildCardMarkup(false);
      card.style.backgroundImage = state.bgCard ? `linear-gradient(rgba(255,255,255,.88),rgba(255,255,255,.88)),url("${state.bgCard}")` : "";
      card.style.backgroundSize = "cover";
      card.style.backgroundPosition = "center";

      const saveButton = $("#pv-save");
      if (saveButton) {
        saveButton.onclick = () => $("#dl").click();
      }

      const qrRoot = $("#qrbox");
      if (qrRoot) {
        drawQRCode(qrRoot, 132);
      }

      const qr = $("#qr");
      if (qr) {
        qr.onclick = () => {
          drawQRCode($("#bigqr"), 260);
          $("#md").classList.add("on");
        };
        qr.onkeydown = (event) => {
          if (event.key === "Enter" || event.key === " ") {
            event.preventDefault();
            qr.click();
          }
        };
      }
    }

    function drawQRCode(target, size) {
      if (!target) return;
      target.innerHTML = "";
      const url = href(state.cardUrl) || "https://example.com";
      try {
        new QRCode(target, {
          text: url,
          width: size,
          height: size,
          correctLevel: QRCode.CorrectLevel.H
        });
      } catch (e) {
        target.textContent = "QR Code 載入失敗";
      }
    }

    function vEscape(value) {
      return String(value || "").replace(/\\\\/g, "\\\\\\\\").replace(/\\n/g, "\\\\n").replace(/,/g, "\\,").replace(/;/g, "\\;");
    }

    function buildVCard() {
      const lines = ["BEGIN:VCARD", "VERSION:3.0", "FN:" + vEscape(state.name || state.en), "N:" + vEscape(state.name || "") + ";;;;"];
      if (state.en) lines.push("NICKNAME:" + vEscape(state.en));
      if (state.company) lines.push("ORG:" + vEscape(state.company));
      if (state.title) lines.push("TITLE:" + vEscape(state.title));
      if (state.phone && !isHidden("phone")) lines.push("TEL;TYPE=CELL:" + state.phone.replace(/[^\\d+]/g, ""));
      if (state.email && !isHidden("email")) lines.push("EMAIL:" + state.email);
      if (state.address) lines.push("ADR;TYPE=WORK:;;" + vEscape(state.address) + ";;;;");
      const cardUrl = href(state.cardUrl);
      if (cardUrl) lines.push("URL:" + cardUrl);
      if (state.bio) lines.push("NOTE:" + vEscape(state.bio));
      if (state.photo && state.photo.startsWith("data:image/jpeg")) {
        lines.push("PHOTO;ENCODING=b;TYPE=JPEG:" + state.photo.split(",")[1]);
      }
      lines.push("END:VCARD");
      return lines.join("\\r\\n");
    }

    function saveFile(filename, data, contentType, message) {
      const done = () => {
        const link = document.createElement("a");
        link.href = URL.createObjectURL(new Blob([data], { type: contentType }));
        link.download = filename;
        document.body.appendChild(link);
        link.click();
        link.remove();
        showToast(message);
      };

      if (typeof claude !== "undefined" && claude.use && /\\.(html|txt|json|md)$/.test(filename)) {
        claude.use("downloads").then((downloads) => {
          if (!downloads) return done();
          downloads.save({
            filename,
            data
          }).then(() => showToast(message), () => {
            showToast("下載失敗，請重試");
          });
        }, () => done());
        return;
      }

      done();
    }

    function copyText(value, successMessage) {
      const fallbackCopy = () => {
        const textarea = document.createElement("textarea");
        textarea.value = value;
        document.body.appendChild(textarea);
        textarea.select();
        try {
          document.execCommand("copy");
          showToast(successMessage);
        } catch (error) {
          showToast("複製失敗");
        }
        textarea.remove();
      };

      if (navigator.clipboard && navigator.clipboard.writeText) {
        navigator.clipboard.writeText(value).then(() => {
          showToast(successMessage);
        }).catch(() => fallbackCopy());
      } else {
        fallbackCopy();
      }
    }

    function initColorPicker() {
      const sw = $("#sw");
      sw.innerHTML = COLORS.map(color =>
        `<button style="background:${color}" data-c="${color}" aria-label="主題色 ${color}" aria-pressed="false"></button>`
      ).join("");

      sw.addEventListener("click", (event) => {
        const colorButton = event.target.closest("[data-c]");
        if (!colorButton) return;
        state.color = colorButton.dataset.c;
        saveState();
        renderCard();
      });
    }

    function initEvents() {
      $("#bk").addEventListener("click", () => goStep(currentStep - 1));
      $("#nx").addEventListener("click", () => goStep(currentStep + 1));
      $("#fab").addEventListener("click", () => {
        const app = $("#app");
        const nextView = app.dataset.view === "edit" ? "view" : "edit";
        app.dataset.view = nextView;
        $("#fab").textContent = nextView === "edit" ? "預覽名片" : "返回編輯";
        window.scrollTo({ top: 0, behavior: "auto" });
      });

      document.addEventListener("click", (event) => {
        const pickerButton = event.target.closest(".pick");
        if (!pickerButton && !event.target.closest(".ico")) {
          closePicker();
        }

        const tab = event.target.closest("[data-st]");
        if (tab) {
          goStep(Number(tab.dataset.st));
        }

        const addButton = event.target.closest("[data-add]");
        if (addButton) {
          const type = addButton.dataset.add;
          const newItem = {
            icon: type === "work" ? "🔗" : "t:LINE",
            label: "",
            url: ""
          };
          if (!Array.isArray(state[type])) state[type] = [];
          state[type].push(newItem);
          saveState();
          renderLinkList(type);
          renderCard();
          const rows = $$("#L-" + type + " .row");
          const inputs = rows[rows.length - 1].querySelectorAll("input");
          if (inputs[0]) inputs[0].focus();
        }

        const uploadButton = event.target.closest("[data-up]");
        if (uploadButton) {
          const key = uploadButton.dataset.up;
          pickFile(key === "photo" ? 480 : key === "logo" ? 300 : 900, (dataUrl) => {
            state[key] = dataUrl;
            saveState();
            fillInputs();
            renderCard();
          });
        }

        const clearButton = event.target.closest("[data-clr]");
        if (clearButton) {
          const key = clearButton.dataset.clr;
          state[key] = "";
          saveState();
          fillInputs();
          renderCard();
        }

        const part = event.target.closest("[data-part]");
        if (part) {
          const key = part.dataset.part;
          state.hide = state.hide || {};
          state.hide[key] = !state.hide[key];
          saveState();
          renderParts();
          renderCard();
        }

        const modal = $("#md");
        if (event.target === modal) {
          modal.classList.remove("on");
        }
      });

      document.addEventListener("input", (event) => {
        const el = event.target;
        const key = el.dataset && el.dataset.k;
        if (!key) return;
        state[key] = el.value;
        saveState();
        renderCard();
      });

      $("#rs").addEventListener("click", () => {
        if (!confirm("要清除目前內容並重設為範例嗎？")) return;
        state = structuredClone(SAMPLE);
        saveState();
        fillInputs();
        renderLinkList("contact");
        renderLinkList("social");
        renderLinkList("work");
        renderBlocks();
        renderParts();
        renderCard();
      });

      $("#dl").addEventListener("click", () => {
        saveFile((state.name || "contact") + ".vcf", buildVCard(), "text/vcard;charset=utf-8", "已產生通訊錄檔");
      });

      $("#cp").addEventListener("click", () => {
        copyText(buildVCard(), "已複製，可貼到記事本存成 .vcf");
      });

      $("#cu").addEventListener("click", () => {
        const url = href(state.cardUrl);
        if (!url) {
          showToast("請先貼上你的專屬網址");
          return;
        }
        copyText(url, "網址已複製");
      });

      $("#dlh").addEventListener("click", () => {
        saveFile("index.html", exportHtml(), "text/html;charset=utf-8", "已下載，接著上傳到免費空間");
      });

      $("#md").addEventListener("click", function () {
        this.classList.remove("on");
      });

      document.addEventListener("keydown", (event) => {
        if (event.key === "Escape") {
          $("#md").classList.remove("on");
          closePicker();
        }
      });
    }

    function exportHtml() {
      const color = state.color || COLORS[0];
      const css = $("#cardcss").textContent;
      const vCard = JSON.stringify(buildVCard()).replace(/</g, "\\u003c");
      const fileName = JSON.stringify((state.name || "contact") + ".vcf");

      return `<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>${escapeHtml((state.name || "") + " " + (state.title || "") + " " + (state.company || ""))}</title>
  <meta property="og:title" content="${escapeHtml((state.name || "") + " | " + (state.company || ""))}">
  <meta property="og:description" content="${escapeHtml(state.bio || "")}">
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&display=swap" rel="stylesheet">
  <style>
    body{margin:0;background:#eceff5;display:flex;justify-content:center;padding:0}
    @media(min-width:520px){body{padding:32px 0}.phone{box-shadow:0 30px 60px -30px rgba(20,30,60,.5)}}
    @media(max-width:519px){.phone{border-radius:0}}
  </style>
  <style>${css}</style>
</head>
<body>
  <div class="phone" id="card" style="--c:${color};${state.bgCard ? `background-image:linear-gradient(rgba(255,255,255,.88),rgba(255,255,255,.88)),url('${state.bgCard}');background-size:cover;background-position:center;` : ""}">
    ${buildCardMarkup(true)}
  </div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"><\\/script>
  <script>
    var V = ${vCard};
    document.getElementById("pv-save").onclick = function () {
      var a = document.createElement("a");
      a.href = URL.createObjectURL(new Blob([V], { type: "text/vcard;charset=utf-8" }));
      a.download = ${fileName};
      document.body.appendChild(a);
      a.click();
      a.remove();
    };
    try {
      new QRCode(document.getElementById("qrbox"), { text: location.href.split("#")[0], width: 132, height: 132, correctLevel: QRCode.CorrectLevel.H });
    } catch (e) {}
  <\\/script>
</body>
</html>`;
    }

    function bootstrap() {
      buildStepButtons();
      initColorPicker();
      initEvents();
      fillInputs();
      renderLinkList("contact");
      renderLinkList("social");
      renderLinkList("work");
      renderBlocks();
      renderParts();
      renderCard();
      goStep(0);
    }

    bootstrap();
  </script>
</body>
</html>
