<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>المحفظة الرقمية - ConnectMe</title>

  <!-- إضافة Telegram Web App SDK -->
  <script src="https://telegram.org/js/telegram-web-app.js"></script>

  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-firestore-compat.js"></script>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    :root {
      --primary: #0072ff;
      --secondary: #ff2d9e;
      --accent: #7d3dff;
      --danger: #ff4757;
      --success: #26a17b;
      --warning: #ff9f00;
      --info: #2d87ff;
      --bg-light: #ffffff;
      --bg-lighter: #f8f9fa;
      --text-dark: #1a1a1a;
      --text-gray: #666;
      --text-light: #888;
      --border-light: rgba(0, 0, 0, 0.1);
      --shadow-light: rgba(0, 0, 0, 0.08);
    }

    body {
      background: #ffffff;
      color: var(--text-dark);
      min-height: 100vh;
      padding: 0;
    }

    /* ========== صفحة المحفظة الرقمية ========== */
    .wallet-page {
      padding: 15px;
      background: #ffffff;
      min-height: 100vh;
    }

    .wallet-frame {
      width: 100%;
      max-width: 420px;
      margin: 0 auto;
      background: #ffffff;
      overflow: hidden;
      position: relative;
    }

    .wallet-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 10px;
      padding: 12px 0;
      border-bottom: 1px solid var(--border-light);
      margin-bottom: 15px;
    }

    .wallet-brand {
      display: flex;
      gap: 12px;
      align-items: center;
    }

    .wallet-avatar {
      width: 52px;
      height: 52px;
      border-radius: 999px;
      background: linear-gradient(135deg, #f8f9fa, #e9ecef);
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      box-shadow: 0 2px 8px var(--shadow-light);
      border: 1px solid rgba(255, 255, 255, 0.8);
    }

    .wallet-coin-icon {
      width: 38px;
      height: 38px;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 700;
      font-size: 16px;
      color: #ffffff;
      background: linear-gradient(90deg, var(--primary), var(--secondary));
      box-shadow: 0 4px 12px rgba(110,0,255,0.2);
      transform-origin: center;
    }

    .wallet-user-meta {
      text-align: right;
    }

    .wallet-user-meta .name {
      font-weight: 700;
      font-size: 14px;
      color: var(--text-dark);
    }

    .wallet-user-meta .sub {
      font-size: 11px;
      color: var(--text-light);
      cursor: pointer;
      transition: all 0.3s ease;
      padding: 2px 6px;
      border-radius: 6px;
    }

    .wallet-user-meta .sub:hover {
      background: rgba(0, 114, 255, 0.1);
      color: var(--primary);
    }

    .wallet-controls {
      display: flex;
      align-items: center;
      gap: 6px;
    }

    .wallet-icon-btn {
      width: 36px;
      height: 36px;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #f8f9fa;
      border: 1px solid var(--border-light);
      cursor: pointer;
      color: var(--primary);
      transition: all 0.3s ease;
    }

    .wallet-icon-btn:hover {
      background: #e9ecef;
      transform: translateY(-1px);
      box-shadow: 0 2px 8px var(--shadow-light);
    }

    .wallet-content {
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .wallet-balance-card {
      background: linear-gradient(135deg, #f8f9fa, #ffffff);
      border-radius: 16px;
      padding: 20px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      border: 1px solid rgba(0, 114, 255, 0.1);
      box-shadow: 0 4px 16px var(--shadow-light);
    }

    .wallet-balance-left {
      text-align: right;
    }

    .wallet-bal-label {
      color: var(--text-light);
      font-size: 12px;
    }

    .wallet-bal-amount {
      font-size: 20px;
      font-weight: 700;
      color: var(--text-dark);
      margin-top: 6px;
      background: linear-gradient(135deg, var(--primary), var(--secondary));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .wallet-bal-trend {
      font-size: 12px;
      color: var(--success);
      background: rgba(38, 161, 123, 0.1);
      padding: 6px 8px;
      border-radius: 10px;
      font-weight: 700;
      border: 1px solid rgba(38, 161, 123, 0.2);
    }

    .wallet-actions {
      display: flex;
      gap: 8px;
    }

    .wallet-neon-btn {
      flex: 1;
      padding: 12px;
      border-radius: 12px;
      border: none;
      font-weight: 700;
      cursor: pointer;
      background: linear-gradient(90deg, var(--primary), var(--secondary));
      color: #ffffff;
      box-shadow: 0 4px 16px rgba(110,0,255,0.2);
      font-size: 14px;
      transition: all 0.3s ease;
    }

    .wallet-neon-btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(110,0,255,0.3);
    }

    /* زر الإرسال الجديد بنفس نمط الأزرار النيون */
    .wallet-send-btn {
      flex: 1;
      padding: 12px;
      border-radius: 12px;
      border: none;
      font-weight: 700;
      cursor: pointer;
      background: linear-gradient(90deg, var(--primary), var(--secondary));
      color: #ffffff;
      box-shadow: 0 4px 16px rgba(110,0,255,0.2);
      font-size: 14px;
      transition: all 0.3s ease;
    }

    .wallet-send-btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(110,0,255,0.3);
    }

    .wallet-ghost-btn {
      flex: 1;
      padding: 12px;
      border-radius: 12px;
      border: 1px solid var(--border-light);
      background: #f8f9fa;
      color: var(--text-gray);
      font-weight: 700;
      cursor: pointer;
      font-size: 14px;
      transition: all 0.3s ease;
    }

    .wallet-ghost-btn:hover {
      background: #e9ecef;
      color: var(--text-dark);
      border-color: var(--primary);
      transform: translateY(-1px);
    }

    .wallet-section-title {
      font-size: 13px;
      color: var(--text-gray);
      margin-bottom: 8px;
      font-weight: 600;
    }

    .wallet-wallets {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .wallet-coin-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 12px;
      border-radius: 12px;
      background: #f8f9fa;
      border: 1px solid transparent;
      transition: all 0.3s ease;
      cursor: pointer;
    }

    .wallet-coin-row:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 16px var(--shadow-light);
      background: #ffffff;
      border-color: var(--primary);
    }

    .wallet-coin-left {
      display: flex;
      gap: 10px;
      align-items: center;
    }

    .wallet-badge {
      width: 42px;
      height: 42px;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 700;
      font-size: 14px;
      color: #ffffff;
      background: linear-gradient(90deg, var(--primary), var(--secondary));
      box-shadow: 0 4px 12px rgba(110,0,255,0.2);
    }

    .wallet-coin-meta {
      text-align: right;
    }

    .wallet-coin-meta .n {
      font-weight: 700;
      font-size: 14px;
      color: var(--text-dark);
    }

    .wallet-coin-meta .s {
      font-size: 11px;
      color: var(--text-light);
      margin-top: 4px;
    }

    .wallet-coin-val {
      text-align: left;
    }

    .wallet-coin-val .qty {
      font-weight: 700;
      font-size: 14px;
      color: var(--text-dark);
    }

    .wallet-coin-val .fiat {
      font-size: 11px;
      color: var(--text-light);
      margin-top: 4px;
    }

    .wallet-tx-list {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .wallet-tx {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      border-radius: 10px;
      background: #f8f9fa;
      border: 1px solid transparent;
      transition: all 0.3s ease;
    }

    .wallet-tx:hover {
      background: #ffffff;
      transform: translateX(-3px);
      border-color: var(--border-light);
    }

    .wallet-tx .left {
      text-align: right;
    }

    .wallet-tx .amt.receive {
      color: var(--success);
      font-weight: 700;
      font-size: 14px;
    }

    .wallet-tx .amt.send {
      color: var(--danger);
      font-weight: 700;
      font-size: 14px;
    }

    .wallet-tx .meta {
      font-size: 11px;
      color: var(--text-light);
    }

    .wallet-smallnote {
      text-align: center;
      color: var(--text-light);
      font-size: 11px;
      padding: 20px 0 10px;
      opacity: 0.7;
    }

    .wallet-search-container {
      position: relative;
      margin-bottom: 10px;
    }

    .wallet-search-input {
      width: 100%;
      padding: 10px 40px 10px 10px;
      border-radius: 10px;
      border: 1px solid var(--border-light);
      background: #ffffff;
      color: var(--text-dark);
      font-family: "Cairo", sans-serif;
      font-size: 14px;
      transition: all 0.3s ease;
    }

    .wallet-search-input:focus {
      outline: none;
      border-color: var(--primary);
      box-shadow: 0 0 0 3px rgba(0,114,255,0.1);
    }

    .wallet-search-input::placeholder {
      color: var(--text-light);
    }

    .wallet-search-icon {
      position: absolute;
      left: 12px;
      top: 50%;
      transform: translateY(-50%);
      color: var(--text-light);
    }

    .wallet-sort-buttons {
      display: flex;
      gap: 8px;
      margin-bottom: 10px;
    }

    .wallet-sort-btn {
      padding: 6px 12px;
      border-radius: 8px;
      border: 1px solid var(--border-light);
      background: #ffffff;
      color: var(--text-gray);
      font-size: 12px;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    .wallet-sort-btn:hover {
      background: #f8f9fa;
      color: var(--text-dark);
    }

    .wallet-sort-btn.active {
      background: linear-gradient(90deg, var(--primary), var(--secondary));
      color: #ffffff;
      font-weight: bold;
      border-color: transparent;
      box-shadow: 0 2px 8px rgba(110,0,255,0.2);
    }

    .wallet-loading-spinner {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(255,255,255,0.9);
      display: none;
      align-items: center;
      justify-content: center;
      z-index: 100;
    }

    .wallet-spinner {
      width: 40px;
      height: 40px;
      border: 3px solid rgba(0,114,255,0.3);
      border-radius: 50%;
      border-top-color: var(--primary);
      animation: spin 1s ease-in-out infinite;
    }

    @keyframes spin {
      to { transform: rotate(360deg); }
    }

    .wallet-modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(255,255,255,0.9);
      backdrop-filter: blur(5px);
      z-index: 80;
      display: none;
      align-items: center;
      justify-content: center;
      padding: 12px;
    }

    .wallet-modal.open {
      display: flex;
    }

    .wallet-modal-card {
      width: 100%;
      max-width: 360px;
      border-radius: 16px;
      padding: 20px;
      background: #ffffff;
      border: 1px solid var(--border-light);
      box-shadow: 0 10px 40px var(--shadow-light);
      max-height: 90vh;
      overflow-y: auto;
    }

    .wallet-modal h3 {
      margin: 0 0 10px 0;
      color: var(--text-dark);
      font-size: 18px;
    }

    .wallet-switch-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 12px 0;
      border-top: 1px solid var(--border-light);
    }

    .wallet-switch-row:first-child {
      border-top: none;
    }

    /* نماذج التحويل والإعدادات */
    .transfer-form {
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .form-group {
      display: flex;
      flex-direction: column;
      gap: 5px;
    }

    .form-label {
      font-size: 14px;
      color: var(--text-dark);
      font-weight: 600;
    }

    .form-select, .form-input {
      padding: 12px;
      border-radius: 10px;
      border: 1px solid var(--border-light);
      background: #ffffff;
      font-size: 14px;
    }

    .exchange-rate {
      background: var(--bg-lighter);
      padding: 10px;
      border-radius: 8px;
      text-align: center;
      font-size: 13px;
      color: var(--text-gray);
      margin: 10px 0;
    }

    .copy-notification {
      position: fixed;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      background: var(--success);
      color: white;
      padding: 10px 20px;
      border-radius: 8px;
      z-index: 1000;
      font-size: 14px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }

    .telegram-status {
      background: rgba(255, 159, 0, 0.1);
      border: 1px solid rgba(255, 159, 0, 0.3);
      border-radius: 10px;
      padding: 10px;
      margin: 10px 0;
      text-align: center;
      font-size: 12px;
      color: var(--warning);
    }

    .positive { color: var(--success); }
    .negative { color: var(--danger); }
    .wallet-coin-row:hover .price-change {
      transform: scale(1.1);
      transition: all 0.3s ease;
    }
  </style>
</head>
<body>
  <!-- ========== صفحة المحفظة الرقمية ========== -->
  <div class="wallet-page">
    <div class="wallet-frame">
      <div class="wallet-header">
        <div class="wallet-brand">
          <div class="wallet-avatar">
            <div class="wallet-coin-icon" id="walletCoinIcon">Z</div>
          </div>
          <div class="wallet-user-meta">
            <div class="name" id="walletUserName">جاري التحميل...</div>
            <div class="sub" id="walletUserId">id: ...</div>
          </div>
        </div>

        <div class="wallet-controls">
          <button class="wallet-icon-btn" id="walletNotifBtn" title="التنبيهات">
            🔔
          </button>

          <button class="wallet-icon-btn" id="walletSettingsBtn" title="الإعدادات">
            ⚙️
          </button>
        </div>
      </div>

      <div class="wallet-content">
        <!-- حالة التليجرام -->
        <div class="telegram-status" id="telegramStatus" style="display: none;">
          🔄 جاري الاتصال بالتليجرام...
        </div>

        <!-- الرصيد -->
        <div class="wallet-balance-card">
          <div class="wallet-balance-left">
            <div class="wallet-bal-label">رصيدك الإجمالي</div>
            <div class="wallet-bal-amount" id="walletTotalBalance">$0.00</div>
          </div>
          <div class="wallet-bal-trend" id="walletTrend">+0.0%</div>
        </div>

        <!-- الأزرار -->
        <div class="wallet-actions">
          <button class="wallet-neon-btn" id="walletTransferBtn">💱 تحويل</button>
          <button class="wallet-neon-btn" id="walletReceiveBtn">📥 استلام</button>
          <button class="wallet-send-btn" id="walletSendBtn">📤 إرسال</button>
        </div>

        <!-- قائمة المحافظ -->
        <div>
          <div class="wallet-section-title">محافظك</div>

          <div class="wallet-search-container">
            <input type="text" class="wallet-search-input" id="walletSearch" placeholder="ابحث في محافظك...">
            <div class="wallet-search-icon">🔍</div>
          </div>

          <div class="wallet-sort-buttons">
            <button class="wallet-sort-btn active" data-sort="default">الافتراضي</button>
            <button class="wallet-sort-btn" data-sort="value-high">الأعلى قيمة</button>
            <button class="wallet-sort-btn" data-sort="value-low">الأقل قيمة</button>
          </div>

          <div class="wallet-wallets" id="walletWalletsList">
            <!-- المحافظ ستظهر هنا ديناميكياً -->
          </div>
        </div>

        <!-- المعاملات -->
        <div>
          <div class="wallet-section-title">آخر المعاملات</div>

          <div class="wallet-search-container">
            <input type="text" class="wallet-search-input" id="walletTransactionSearch" placeholder="ابحث في المعاملات...">
            <div class="wallet-search-icon">🔍</div>
          </div>

          <div class="wallet-tx-list" id="walletTransactionList">
            <!-- المعاملات ستظهر هنا ديناميكياً -->
          </div>
        </div>

        <div class="wallet-smallnote">© 2025 ConnectMe — المحفظة الرقمية</div>
      </div>
    </div>

    <!-- Loading Spinner -->
    <div class="wallet-loading-spinner" id="walletLoadingSpinner">
      <div class="wallet-spinner"></div>
    </div>

    <!-- نافذة تحويل العملات -->
    <div class="wallet-modal" id="walletTransferModal">
      <div class="wallet-modal-card">
        <h3>تحويل العملات</h3>
        <div style="font-size:13px;color:var(--text-light);margin-bottom:10px">تحويل بين العملات المتاحة</div>

        <div class="transfer-form">
          <div class="form-group">
            <label class="form-label">من العملة</label>
            <select class="form-select" id="fromCurrency">
              <option value="BTC">BTC - رصيدك: 0.0052</option>
              <option value="ETH">ETH - رصيدك: 0.1250</option>
              <option value="BNB">BNB - رصيدك: 1.5000</option>
              <option value="XRP">XRP - رصيدك: 1500.00</option>
              <option value="ADA">ADA - رصيدك: 2500.00</option>
              <option value="SOL">SOL - رصيدك: 8.5000</option>
              <option value="DOT">DOT - رصيدك: 120.00</option>
              <option value="DOGE">DOGE - رصيدك: 5000.00</option>
              <option value="ZST">ZST - رصيدك: 100.0000</option>
              <option value="USDT">USDT - رصيدك: 25.5000</option>
            </select>
          </div>

          <div class="form-group">
            <label class="form-label">إلى العملة</label>
            <select class="form-select" id="toCurrency">
              <option value="USDT">USDT - رصيدك: 25.5000</option>
              <option value="BTC">BTC - رصيدك: 0.0052</option>
              <option value="ETH">ETH - رصيدك: 0.1250</option>
              <option value="BNB">BNB - رصيدك: 1.5000</option>
              <option value="XRP">XRP - رصيدك: 1500.00</option>
              <option value="ADA">ADA - رصيدك: 2500.00</option>
              <option value="SOL">SOL - رصيدك: 8.5000</option>
              <option value="DOT">DOT - رصيدك: 120.00</option>
              <option value="DOGE">DOGE - رصيدك: 5000.00</option>
              <option value="ZST">ZST - رصيدك: 100.0000</option>
            </select>
          </div>

          <div class="form-group">
            <label class="form-label">المبلغ</label>
            <input type="number" class="form-input" id="transferAmount" placeholder="أدخل المبلغ">
          </div>

          <div class="exchange-rate" id="exchangeRateDisplay">
            سعر الصرف: جاري التحميل...
          </div>

          <div style="display:flex;gap:8px;margin-top:12px;justify-content:flex-end">
            <button id="walletConfirmTransfer" class="wallet-neon-btn" style="padding:8px 12px;font-size:13px">تأكيد التحويل</button>
            <button id="walletCancelTransfer" class="wallet-ghost-btn" style="padding:8px 12px;font-size:13px">إلغاء</button>
          </div>
        </div>
      </div>
    </div>

    <!-- نافذة إرسال الأموال -->
    <div class="wallet-modal" id="walletSendModal">
      <div class="wallet-modal-card">
        <h3>إرسال الأموال</h3>
        <div style="font-size:13px;color:var(--text-light);margin-bottom:10px">إرسال أموال إلى مستخدم آخر عبر المعرف</div>

        <div class="transfer-form">
          <div class="form-group">
            <label class="form-label">من العملة</label>
            <select class="form-select" id="sendFromCurrency">
              <option value="BTC">BTC - رصيدك: 0.0052</option>
              <option value="ETH">ETH - رصيدك: 0.1250</option>
              <option value="BNB">BNB - رصيدك: 1.5000</option>
              <option value="XRP">XRP - رصيدك: 1500.00</option>
              <option value="ADA">ADA - رصيدك: 2500.00</option>
              <option value="SOL">SOL - رصيدك: 8.5000</option>
              <option value="DOT">DOT - رصيدك: 120.00</option>
              <option value="DOGE">DOGE - رصيدك: 5000.00</option>
              <option value="ZST">ZST - رصيدك: 100.0000</option>
              <option value="USDT">USDT - رصيدك: 25.5000</option>
            </select>
          </div>

          <div class="form-group">
            <label class="form-label">إلى معرف المستخدم</label>
            <input type="text" class="form-input" id="sendToUserId" placeholder="أدخل معرف المستخدم">
          </div>

          <div class="form-group">
            <label class="form-label">المبلغ</label>
            <input type="number" class="form-input" id="sendAmount" placeholder="أدخل المبلغ">
          </div>

          <div style="display:flex;gap:8px;margin-top:12px;justify-content:flex-end">
            <button id="walletConfirmSend" class="wallet-neon-btn" style="padding:8px 12px;font-size:13px">تأكيد الإرسال</button>
            <button id="walletCancelSend" class="wallet-ghost-btn" style="padding:8px 12px;font-size:13px">إلغاء</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    // ========== الإعدادات الأساسية ==========
    const BOT_TOKEN = "8323306928:AAG0Fkt0gPgrEJEFmf3itqyk2A7Xh8ZgVYI";

    // إعدادات Firebase
    const firebaseConfig = {
      apiKey: "AIzaSyBX8v0qIVjG-Z46fVDeEUDKcPxa_FQF9Qc",
      authDomain: "bnak-belaiba.firebaseapp.com",
      projectId: "bnak-belaiba",
      storageBucket: "bnak-belaiba.firebasestorage.app",
      messagingSenderId: "286736432059",
      appId: "1:286736432059:web:ec1ed4292850f00008229e"
    };

    // ========== تهيئة Firebase ==========
    let db;
    try {
      firebase.initializeApp(firebaseConfig);
      db = firebase.firestore();
      console.log('✅ Firebase initialized successfully');
    } catch (error) {
      console.error('❌ Firebase initialization error:', error);
    }

    // ========== بيانات التطبيق ==========
    let currentUser = {
      id: null,
      firstName: 'مستخدم',
      lastName: '',
      username: null
    };

    // بيانات العملات الرقمية الحقيقية
    const coinInfo = {
      'BTC': { name: 'Bitcoin', icon: '฿', price: 67450, change: 2.3 },
      'ETH': { name: 'Ethereum', icon: 'Ξ', price: 3450, change: 1.8 },
      'BNB': { name: 'Binance Coin', icon: 'B', price: 585, change: 0.5 },
      'XRP': { name: 'Ripple', icon: 'X', price: 0.52, change: -0.3 },
      'ADA': { name: 'Cardano', icon: 'A', price: 0.45, change: 1.2 },
      'SOL': { name: 'Solana', icon: 'S', price: 165, change: 4.7 },
      'DOT': { name: 'Polkadot', icon: 'D', price: 6.90, change: 0.9 },
      'DOGE': { name: 'Dogecoin', icon: 'Ð', price: 0.12, change: -1.5 },
      'ZST': { name: 'ZST Coin', icon: 'Z', price: 0.0002, change: 0.0 },
      'USDT': { name: 'Tether', icon: '＄', price: 1.00, change: 0.0 }
    };

    // أرصدة افتراضية
    const userBalances = {
      'BTC': 0.0052,
      'ETH': 0.125,
      'BNB': 1.5,
      'XRP': 1500,
      'ADA': 2500,
      'SOL': 8.5,
      'DOT': 120,
      'DOGE': 5000,
      'ZST': 100.0000,
      'USDT': 25.50
    };

    // أسعار الصرف الحية
    let exchangeRates = {
      'BTC_USDT': 67450, 'BTC_ETH': 19.55, 'BTC_BNB': 115.3, 'BTC_XRP': 129711, 'BTC_ADA': 149888, 'BTC_SOL': 408, 'BTC_DOT': 9775, 'BTC_DOGE': 562083, 'BTC_ZST': 337250000,
      'ETH_USDT': 3450, 'ETH_BTC': 0.0511, 'ETH_BNB': 5.9, 'ETH_XRP': 6634, 'ETH_ADA': 7666, 'ETH_SOL': 20.9, 'ETH_DOT': 500, 'ETH_DOGE': 28750, 'ETH_ZST': 17250000,
      'BNB_USDT': 585, 'BNB_BTC': 0.00867, 'BNB_ETH': 0.169, 'BNB_XRP': 1125, 'BNB_ADA': 1300, 'BNB_SOL': 3.54, 'BNB_DOT': 84.78, 'BNB_DOGE': 4875, 'BNB_ZST': 2925000,
      'XRP_USDT': 0.52, 'XRP_BTC': 0.0000077, 'XRP_ETH': 0.000151, 'XRP_BNB': 0.000889, 'XRP_ADA': 0.865, 'XRP_SOL': 0.00315, 'XRP_DOT': 0.0754, 'XRP_DOGE': 4.33, 'XRP_ZST': 2600,
      'ADA_USDT': 0.45, 'ADA_BTC': 0.00000667, 'ADA_ETH': 0.000130, 'ADA_BNB': 0.000769, 'ADA_XRP': 1.156, 'ADA_SOL': 0.00273, 'ADA_DOT': 0.0652, 'ADA_DOGE': 3.75, 'ADA_ZST': 2250,
      'SOL_USDT': 165, 'SOL_BTC': 0.00245, 'SOL_ETH': 0.0478, 'SOL_BNB': 0.282, 'SOL_XRP': 317, 'SOL_ADA': 366, 'SOL_DOT': 23.91, 'SOL_DOGE': 1375, 'SOL_ZST': 825000,
      'DOT_USDT': 6.90, 'DOT_BTC': 0.000102, 'DOT_ETH': 0.002, 'DOT_BNB': 0.0118, 'DOT_XRP': 13.27, 'DOT_ADA': 15.33, 'DOT_SOL': 0.0418, 'DOT_DOGE': 57.5, 'DOT_ZST': 34500,
      'DOGE_USDT': 0.12, 'DOGE_BTC': 0.00000178, 'DOGE_ETH': 0.0000348, 'DOGE_BNB': 0.000205, 'DOGE_XRP': 0.231, 'DOGE_ADA': 0.267, 'DOGE_SOL': 0.000727, 'DOGE_DOT': 0.0174, 'DOGE_ZST': 600,
      'ZST_USDT': 0.0002, 'ZST_BTC': 0.00000000296, 'ZST_ETH': 0.000000058, 'ZST_BNB': 0.000000342, 'ZST_XRP': 0.000385, 'ZST_ADA': 0.000444, 'ZST_SOL': 0.00000121, 'ZST_DOT': 0.000029, 'ZST_DOGE': 0.00167,
      'USDT_BTC': 0.0000148, 'USDT_ETH': 0.000290, 'USDT_BNB': 0.00171, 'USDT_XRP': 1.923, 'USDT_ADA': 2.222, 'USDT_SOL': 0.00606, 'USDT_DOT': 0.1449, 'USDT_DOGE': 8.333, 'USDT_ZST': 5000
    };

    // ========== دوال Telegram WebApp ==========
    function initializeTelegramWebApp() {
      try {
        if (window.Telegram && Telegram.WebApp) {
          const tg = Telegram.WebApp;
          console.log('✅ Telegram WebApp detected');

          // توسيع الواجهة
          tg.expand();
          tg.setBackgroundColor('#ffffff');

          // جلب بيانات المستخدم
          const user = tg.initDataUnsafe.user;
          console.log('Telegram user data:', user);

          if (user && user.id) {
            document.getElementById('telegramStatus').style.display = 'block';

            currentUser = {
              id: user.id,
              firstName: user.first_name || 'مستخدم',
              lastName: user.last_name || '',
              username: user.username
            };

            console.log('✅ User data loaded:', currentUser);

            // تحديث الواجهة فوراً
            updateUserInterface(currentUser);

            // تسجيل المستخدم في Firebase
            registerUser(currentUser);

          } else {
            useMockData();
          }

        } else {
          useMockData();
        }
      } catch (error) {
        console.error('❌ Telegram WebApp Error:', error);
        useMockData();
      }
    }

    // استخدام بيانات تجريبية للاختبار
    function useMockData() {
      const mockUser = {
        id: 5922049376,
        firstName: 'Akram',
        lastName: 'Khezzari', 
        username: 'A_mja_d_0_5'
      };

      currentUser = mockUser;
      updateUserInterface(mockUser);
      registerUser(mockUser);
    }

    // ========== دوال Firebase ==========
    async function registerUser(userData) {
      showWalletLoading();

      try {
        if (!db) {
          console.log('❌ Firebase not available - using real-time data');
          // عرض البيانات الحقيقية
          updateWalletsListRealTime();
          updateTotalBalance();
          hideWalletLoading();
          return;
        }

        const userRef = db.collection('users').doc(userData.id.toString());
        const userDoc = await userRef.get();

        if (!userDoc.exists) {
          // مستخدم جديد
          const newUser = {
            ...userData,
            registrationDate: firebase.firestore.FieldValue.serverTimestamp(),
            balance: calculateTotalBalance(),
            wallets: userBalances,
            transactions: [{
              type: 'receive',
              amount: 100.00,
              currency: 'ZST',
              description: 'إيداع ZST ابتدائي',
              timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }]
          };

          await userRef.set(newUser);
          console.log('✅ New user registered:', userData.id);

        } else {
          console.log('✅ User already exists:', userData.id);
        }

        // تحميل بيانات المستخدم
        loadUserData(userData.id);

      } catch (error) {
        console.error('❌ Error registering user:', error);
        updateWalletsListRealTime();
        updateTotalBalance();
      } finally {
        hideWalletLoading();
      }
    }

    // تحميل بيانات المستخدم
    async function loadUserData(userId) {
      try {
        if (!db) {
          updateWalletsListRealTime();
          updateTotalBalance();
          return;
        }

        const userDoc = await db.collection('users').doc(userId.toString()).get();

        if (userDoc.exists) {
          const userData = userDoc.data();
          updateWalletData(userData);
        } else {
          updateWalletsListRealTime();
          updateTotalBalance();
        }
      } catch (error) {
        console.error('❌ Error loading user data:', error);
        updateWalletsListRealTime();
        updateTotalBalance();
      }
    }

    // تحديث بيانات المحفظة
    function updateWalletData(userData) {
      // تحديث الرصيد الإجمالي
      updateTotalBalance();

      // تحديث قائمة المحافظ
      updateWalletsListRealTime();

      // تحديث المعاملات
      updateTransactionsList(userData.transactions || []);
    }

    // ========== تحديث واجهة المحفظة مع البيانات الحقيقية ==========
    function updateWalletsListRealTime() {
      const walletsList = document.getElementById('walletWalletsList');
      walletsList.innerHTML = '';

      Object.entries(coinInfo).forEach(([symbol, info]) => {
        const balance = userBalances[symbol] || 0;
        const usdValue = balance * info.price;
        const changeClass = info.change >= 0 ? 'positive' : 'negative';
        const changeIcon = info.change >= 0 ? '↗' : '↘';

        const walletElement = document.createElement('div');
        walletElement.className = 'wallet-coin-row';
        walletElement.innerHTML = `
          <div class="wallet-coin-left">
            <div class="wallet-badge" style="background: linear-gradient(135deg, ${getCoinColor(symbol)});">
              ${info.icon}
            </div>
            <div class="wallet-coin-meta">
              <div class="n">${info.name}</div>
              <div class="s">${symbol}</div>
              <div class="price-change ${changeClass}" style="font-size:10px;margin-top:2px;">
                ${changeIcon} ${Math.abs(info.change).toFixed(1)}%
              </div>
            </div>
          </div>
          <div class="wallet-coin-val">
            <div class="qty">${formatBalance(balance, symbol)} ${symbol}</div>
            <div class="fiat">≈ $${usdValue.toFixed(2)}</div>
            <div class="price" style="font-size:10px;color:var(--text-light);margin-top:2px">
              $${formatPrice(info.price)}
            </div>
          </div>
        `;
        walletsList.appendChild(walletElement);
      });
    }

    // تحديث الرصيد الإجمالي
    function updateTotalBalance() {
      let totalBalance = 0;
      let totalChange = 0;
      let count = 0;

      Object.entries(userBalances).forEach(([symbol, balance]) => {
        const price = coinInfo[symbol]?.price || 0;
        const change = coinInfo[symbol]?.change || 0;
        totalBalance += balance * price;
        totalChange += change;
        count++;
      });

      const avgChange = count > 0 ? totalChange / count : 0;

      document.getElementById('walletTotalBalance').textContent = `$${totalBalance.toFixed(2)}`;
      document.getElementById('walletTrend').textContent = `${avgChange >= 0 ? '+' : ''}${avgChange.toFixed(1)}%`;
      document.getElementById('walletTrend').style.color = avgChange >= 0 ? 'var(--success)' : 'var(--danger)';
    }

    // حساب الرصيد الإجمالي
    function calculateTotalBalance() {
      let total = 0;
      Object.entries(userBalances).forEach(([symbol, balance]) => {
        total += balance * (coinInfo[symbol]?.price || 0);
      });
      return total;
    }

    // دوال مساعدة لتنسيق الأرقام
    function formatBalance(balance, symbol) {
      if (symbol === 'BTC' || symbol === 'ETH') return balance.toFixed(6);
      if (symbol === 'USDT') return balance.toFixed(2);
      if (balance < 1) return balance.toFixed(4);
      if (balance < 1000) return balance.toFixed(2);
      return balance.toFixed(0);
    }

    function formatPrice(price) {
      if (price < 0.01) return price.toFixed(6);
      if (price < 1) return price.toFixed(4);
      if (price < 1000) return price.toFixed(2);
      return price.toFixed(0);
    }

    function getCoinColor(symbol) {
      const colors = {
        'BTC': '#f7931a, #ffc350',
        'ETH': '#627eea, #8fa3e9',
        'BNB': '#f3ba2f, #f8d156',
        'XRP': '#23292f, #4a5568',
        'ADA': '#0033ad, #306ccc',
        'SOL': '#00ffbd, #66ffd1',
        'DOT': '#e6007a, #ff4da6',
        'DOGE': '#c2a633, #e6d16c',
        'ZST': '#0072ff, #ff2d9e',
        'USDT': '#26a17b, #4cd4a7'
      };
      return colors[symbol] || '#0072ff, #ff2d9e';
    }

    // تحديث قائمة المعاملات
    function updateTransactionsList(transactions) {
      const transactionsList = document.getElementById('walletTransactionList');
      transactionsList.innerHTML = '';

      if (!transactions || transactions.length === 0) {
        transactionsList.innerHTML = '<div style="text-align:center;color:var(--text-light);padding:20px;">لا توجد معاملات حتى الآن</div>';
        return;
      }

      // ترتيب المعاملات من الأحدث إلى الأقدم
      const sortedTransactions = [...transactions].sort((a, b) => {
        const timeA = a.timestamp?.toDate ? a.timestamp.toDate() : new Date(a.timestamp);
        const timeB = b.timestamp?.toDate ? b.timestamp.toDate() : new Date(b.timestamp);
        return timeB - timeA;
      });

      sortedTransactions.forEach(tx => {
        const txElement = document.createElement('div');
        txElement.className = 'wallet-tx';

        let description = tx.description;
        if (tx.type === 'send' && tx.toUserId) {
          description = `إرسال إلى ${tx.toUserId}`;
        } else if (tx.type === 'receive' && tx.fromUserId) {
          description = `استلام من ${tx.fromUserId}`;
        } else if (tx.type === 'convert') {
          description = `تحويل ${tx.currency} إلى ${tx.convertedCurrency}`;
        }

        const time = formatTime(tx.timestamp);

        txElement.innerHTML = `
          <div class="left">
            <div class="meta">${description} • ${time}</div>
            <div class="meta">${tx.currency}${tx.convertedCurrency ? ' → ' + tx.convertedCurrency : ''}</div>
          </div>
          <div class="amt ${tx.type}">${tx.type === 'receive' ? '+' : '-'}${tx.amount.toFixed(2)}</div>
        `;
        transactionsList.appendChild(txElement);
      });
    }

    // تنسيق الوقت
    function formatTime(timestamp) {
      if (!timestamp) return 'الآن';

      try {
        const date = timestamp.toDate ? timestamp.toDate() : new Date(timestamp);
        const now = new Date();
        const diffMs = now - date;
        const diffMins = Math.floor(diffMs / 60000);
        const diffHours = Math.floor(diffMs / 3600000);

        if (diffMins < 1) return 'الآن';
        if (diffMins < 60) return `قبل ${diffMins} دقيقة`;
        if (diffHours < 24) return `قبل ${diffHours} ساعة`;
        return date.toLocaleDateString('ar-EG');
      } catch (error) {
        return 'الآن';
      }
    }

    // ========== تحديث الأسعار الحية ==========
    function startLivePriceUpdates() {
      // تحديث الأسعار كل 30 ثانية
      setInterval(() => {
        updateLivePrices();
      }, 30000);

      // تحديث أولي
      updateLivePrices();
    }

    function updateLivePrices() {
      // محاكاة تغيرات الأسعار الحية
      Object.keys(coinInfo).forEach(symbol => {
        if (symbol !== 'USDT' && symbol !== 'ZST') {
          const change = (Math.random() - 0.5) * 2; // تغيير عشوائي بين -1% إلى +1%
          coinInfo[symbol].price = coinInfo[symbol].price * (1 + change/100);
          coinInfo[symbol].change += change;

          // تحديث أسعار الصرف المرتبطة
          updateExchangeRates(symbol);
        }
      });

      // تحديث واجهة المستخدم
      updateWalletsListRealTime();
      updateTotalBalance();

      // تحديث سعر الصرف في نافذة التحويل إذا كانت مفتوحة
      if (document.getElementById('walletTransferModal').classList.contains('open')) {
        updateExchangeRate();
      }
    }

    function updateExchangeRates(baseSymbol) {
      Object.keys(coinInfo).forEach(targetSymbol => {
        if (baseSymbol !== targetSymbol) {
          const rateKey = `${baseSymbol}_${targetSymbol}`;
          const reverseRateKey = `${targetSymbol}_${baseSymbol}`;

          if (coinInfo[baseSymbol] && coinInfo[targetSymbol]) {
            exchangeRates[rateKey] = coinInfo[baseSymbol].price / coinInfo[targetSymbol].price;
            exchangeRates[reverseRateKey] = 1 / exchangeRates[rateKey];
          }
        }
      });
    }

    // ========== دوال التحويل بين العملات ==========
    function updateExchangeRate() {
      const from = document.getElementById('fromCurrency').value;
      const to = document.getElementById('toCurrency').value;

      if (from === to) {
        document.getElementById('exchangeRateDisplay').textContent = "لا يمكن التحويل بين نفس العملة";
        return;
      }

      const rateKey = `${from}_${to}`;
      const rate = exchangeRates[rateKey];
      const amount = parseFloat(document.getElementById('transferAmount').value) || 1;
      const converted = amount * rate;

      if (rate) {
        document.getElementById('exchangeRateDisplay').textContent = 
          `1 ${from} = ${rate.toFixed(8)} ${to} | ${amount} ${from} = ${converted.toFixed(8)} ${to}`;
      } else {
        document.getElementById('exchangeRateDisplay').textContent = "سعر الصرف غير متوفر";
      }
    }

    async function convertCurrency(fromCurrency, toCurrency, amount) {
      showWalletLoading();

      try {
        if (!db) {
          alert('❌ قاعدة البيانات غير متاحة حالياً');
          return;
        }

        const userRef = db.collection('users').doc(currentUser.id.toString());
        const userDoc = await userRef.get();

        if (!userDoc.exists) {
          alert('❌ المستخدم غير موجود');
          return;
        }

        const userData = userDoc.data();
        const fromBalance = userData.wallets[fromCurrency] || 0;

        if (fromBalance < amount) {
          alert('❌ رصيدك غير كافي');
          return;
        }

        const rateKey = `${fromCurrency}_${toCurrency}`;
        const rate = exchangeRates[rateKey];

        if (!rate) {
          alert('❌ سعر الصرف غير متوفر');
          return;
        }

        const convertedAmount = amount * rate;

        // تحديث الرصيد
        await userRef.update({
          [`wallets.${fromCurrency}`]: fromBalance - amount,
          [`wallets.${toCurrency}`]: (userData.wallets[toCurrency] || 0) + convertedAmount
        });

        // تحديث الأرصدة المحلية
        userBalances[fromCurrency] = fromBalance - amount;
        userBalances[toCurrency] = (userData.wallets[toCurrency] || 0) + convertedAmount;

        // إضافة معاملة
        const transaction = {
          type: 'convert',
          amount: amount,
          currency: fromCurrency,
          convertedAmount: convertedAmount,
          convertedCurrency: toCurrency,
          description: `تحويل ${fromCurrency} إلى ${toCurrency}`,
          timestamp: firebase.firestore.FieldValue.serverTimestamp()
        };

        await userRef.update({
          transactions: firebase.firestore.FieldValue.arrayUnion(transaction)
        });

        showCopyNotification(`✅ تم تحويل ${amount} ${fromCurrency} إلى ${convertedAmount.toFixed(4)} ${toCurrency}`);
        closeTransferModal();

        // إعادة تحميل البيانات
        updateWalletsListRealTime();
        updateTotalBalance();

      } catch (error) {
        console.error('❌ Error converting currency:', error);
        alert('حدث خطأ أثناء التحويل');
      } finally {
        hideWalletLoading();
      }
    }

    // ========== دوال الإرسال ==========
    async function sendMoney(toUserId, currency, amount) {
      showWalletLoading();

      try {
        if (!db) {
          alert('❌ قاعدة البيانات غير متاحة حالياً');
          return;
        }

        // التحقق من رصيد المرسل
        const senderRef = db.collection('users').doc(currentUser.id.toString());
        const senderDoc = await senderRef.get();

        if (!senderDoc.exists) {
          alert('❌ المستخدم المرسل غير موجود');
          return;
        }

        const senderData = senderDoc.data();
        const senderBalance = senderData.wallets[currency] || 0;

        if (senderBalance < amount) {
          alert('❌ رصيدك غير كافي');
          return;
        }

        // التحقق من وجود المستلم
        const receiverRef = db.collection('users').doc(toUserId.toString());
        const receiverDoc = await receiverRef.get();

        if (!receiverDoc.exists) {
          alert('❌ المستخدم المستقبل غير موجود');
          return;
        }

        // تنفيذ التحويل
        const batch = db.batch();

        // خصم من المرسل
        batch.update(senderRef, {
          [`wallets.${currency}`]: senderBalance - amount
        });

        // إضافة معاملة للمرسل
        const senderTransaction = {
          type: 'send',
          amount: amount,
          currency: currency,
          toUserId: toUserId,
          description: `إرسال إلى ${toUserId}`,
          timestamp: firebase.firestore.FieldValue.serverTimestamp()
        };
        batch.update(senderRef, {
          transactions: firebase.firestore.FieldValue.arrayUnion(senderTransaction)
        });

        // إضافة للمستلم
        const receiverData = receiverDoc.data();
        const receiverBalance = receiverData.wallets[currency] || 0;

        batch.update(receiverRef, {
          [`wallets.${currency}`]: receiverBalance + amount
        });

        // إضافة معاملة للمستلم
        const receiverTransaction = {
          type: 'receive',
          amount: amount,
          currency: currency,
          fromUserId: currentUser.id,
          description: `استلام من ${currentUser.id}`,
          timestamp: firebase.firestore.FieldValue.serverTimestamp()
        };
        batch.update(receiverRef, {
          transactions: firebase.firestore.FieldValue.arrayUnion(receiverTransaction)
        });

        await batch.commit();

        // تحديث الأرصدة المحلية
        userBalances[currency] = senderBalance - amount;

        // إرسال إشعارات
        await sendTelegramMessage(currentUser.id, `✅ تم إرسال ${amount} ${currency} إلى المستخدم ${toUserId}`);
        await sendTelegramMessage(toUserId, `🎉 استلمت ${amount} ${currency} من المستخدم ${currentUser.id}`);

        showCopyNotification(`✅ تم إرسال ${amount} ${currency} بنجاح`);
        closeSendModal();

        // إعادة تحميل البيانات
        updateWalletsListRealTime();
        updateTotalBalance();

      } catch (error) {
        console.error('❌ Error sending money:', error);
        alert('حدث خطأ أثناء الإرسال');
      } finally {
        hideWalletLoading();
      }
    }

    async function sendTelegramMessage(chatId, text) {
      try {
        await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({
            chat_id: chatId,
            text: text
          })
        });
      } catch (error) {
        console.error('Error sending Telegram message:', error);
      }
    }

    // ========== دوال الواجهة ==========
    function showWalletLoading() {
      document.getElementById('walletLoadingSpinner').style.display = 'flex';
    }

    function hideWalletLoading() {
      document.getElementById('walletLoadingSpinner').style.display = 'none';
    }

    function openTransferModal() {
      document.getElementById('walletTransferModal').classList.add('open');
      updateExchangeRate();
    }

    function closeTransferModal() {
      document.getElementById('walletTransferModal').classList.remove('open');
      document.getElementById('transferAmount').value = '';
    }

    function openSendModal() {
      document.getElementById('walletSendModal').classList.add('open');
    }

    function closeSendModal() {
      document.getElementById('walletSendModal').classList.remove('open');
      document.getElementById('sendToUserId').value = '';
      document.getElementById('sendAmount').value = '';
    }

    // دالة نسخ المعرف
    function copyUserId(userId) {
      if (navigator.clipboard && navigator.clipboard.writeText) {
        navigator.clipboard.writeText(userId.toString()).then(() => {
          showCopyNotification('تم نسخ المعرف: ' + userId);
        }).catch(err => {
          fallbackCopy(userId);
        });
      } else {
        fallbackCopy(userId);
      }
    }

    function fallbackCopy(userId) {
      const textArea = document.createElement('textarea');
      textArea.value = userId.toString();
      document.body.appendChild(textArea);
      textArea.select();
      document.execCommand('copy');
      document.body.removeChild(textArea);
      showCopyNotification('تم نسخ المعرف: ' + userId);
    }

    // إظهار إشعار النسخ
    function showCopyNotification(message) {
      const notification = document.createElement('div');
      notification.className = 'copy-notification';
      notification.textContent = message;
      document.body.appendChild(notification);

      setTimeout(() => {
        notification.remove();
      }, 2000);
    }

    // تحديث واجهة المستخدم
    function updateUserInterface(userData) {
      const userName = userData.firstName + (userData.lastName ? ' ' + userData.lastName : '');
      document.getElementById('walletUserName').textContent = userName;
      document.getElementById('walletUserId').textContent = `id: ${userData.id}`;
      document.getElementById('walletUserId').setAttribute('onclick', `copyUserId(${userData.id})`);

      // إخفاء حالة التليجرام بعد تحميل البيانات
      setTimeout(() => {
        document.getElementById('telegramStatus').style.display = 'none';
      }, 2000);
    }

    // ========== التهيئة ==========
    document.addEventListener('DOMContentLoaded', function() {
      console.log('🚀 Initializing Telegram WebApp...');
      initializeTelegramWebApp();

      // بدء تحديث الأسعار الحية
      startLivePriceUpdates();

      // أحداث الأزرار
      document.getElementById('walletTransferBtn').addEventListener('click', openTransferModal);
      document.getElementById('walletSendBtn').addEventListener('click', openSendModal);

      document.getElementById('walletCancelTransfer').addEventListener('click', closeTransferModal);
      document.getElementById('walletCancelSend').addEventListener('click', closeSendModal);

      // أحداث تحديث سعر الصرف
      document.getElementById('fromCurrency').addEventListener('change', updateExchangeRate);
      document.getElementById('toCurrency').addEventListener('change', updateExchangeRate);
      document.getElementById('transferAmount').addEventListener('input', updateExchangeRate);

      // تأكيد التحويل
      document.getElementById('walletConfirmTransfer').addEventListener('click', function() {
        const fromCurrency = document.getElementById('fromCurrency').value;
        const toCurrency = document.getElementById('toCurrency').value;
        const amount = parseFloat(document.getElementById('transferAmount').value);

        if (!amount || amount <= 0) {
          alert('يرجى إدخال مبلغ صحيح');
          return;
        }

        if (fromCurrency === toCurrency) {
          alert('لا يمكن التحويل بين نفس العملة');
          return;
        }

        convertCurrency(fromCurrency, toCurrency, amount);
      });

      // تأكيد الإرسال
      document.getElementById('walletConfirmSend').addEventListener('click', function() {
        const toUserId = document.getElementById('sendToUserId').value;
        const currency = document.getElementById('sendFromCurrency').value;
        const amount = parseFloat(document.getElementById('sendAmount').value);

        if (!toUserId || !amount) {
          alert('يرجى ملء جميع الحقول');
          return;
        }

        if (amount <= 0) {
          alert('يرجى إدخال مبلغ صحيح');
          return;
        }

        sendMoney(toUserId, currency, amount);
      });

      // إغلاق النوافذ بالنقر خارجها
      document.getElementById('walletTransferModal').addEventListener('click', function(e) {
        if (e.target === this) {
          closeTransferModal();
        }
      });

      document.getElementById('walletSendModal').addEventListener('click', function(e) {
        if (e.target === this) {
          closeSendModal();
        }
      });

      // زر الاستلام
      document.getElementById('walletReceiveBtn').addEventListener('click', function() {
        showCopyNotification(`📥 معرفك: ${currentUser.id} - يمكن للآخرين استخدامه لإرسال الأموال لك`);
      });
    });
  </script>
</body>
</html>