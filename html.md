<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>スマホ料金シミュレーター</title>
    <style>
        /* --- ベース設定（老若男女問わず読みやすいゴシック体と清潔感のある配色） --- */
        :root {
            --bg-color: #f4f6f9;
            --card-bg: #ffffff;
            --primary-color: #1d4ed8;
            --primary-hover: #1e40af;
            --primary-light: #eff6ff;
            --text-main: #1f2937;
            --text-sub: #4b5563;
            --border-color: #d1d5db;
            --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.08);
            --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.06);
        }

        * {
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Hiragino Kaku Gothic ProN", "Noto Sans JP", "Yu Gothic", sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            margin: 0;
            padding: 32px 16px;
            line-height: 1.6;
            display: flex;
            justify-content: center;
        }

        /* --- メインコンテナ --- */
        .app-container {
            max-width: 920px;
            width: 100%;
            background-color: var(--card-bg);
            border-radius: 16px;
            box-shadow: var(--shadow-md);
            padding: 36px 32px;
            border: 1px solid #e5e7eb;
        }
        @media (max-width: 600px) {
            .app-container {
                padding: 24px 18px;
            }
        }

        /* --- ヘッダー --- */
        .app-header {
            text-align: center;
            margin-bottom: 32px;
            padding-bottom: 20px;
            border-bottom: 2px solid #f3f4f6;
        }
        .app-header h1 {
            margin: 0 0 8px 0;
            font-size: 24px;
            font-weight: 700;
            color: #111827;
            letter-spacing: 0.03em;
        }
        .app-header p {
            margin: 0;
            font-size: 14px;
            color: var(--text-sub);
        }

        /* --- 入力フォームグリッド --- */
        .form-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 28px;
        }
        @media (max-width: 700px) {
            .form-grid {
                grid-template-columns: 1fr;
            }
        }

        .form-card {
            background-color: #f9fafb;
            border: 1px solid #e5e7eb;
            border-radius: 12px;
            padding: 18px 20px;
        }
        .form-card.full-width {
            grid-column: 1 / -1;
        }

        .form-label {
            display: block;
            font-weight: 700;
            font-size: 14px;
            color: #374151;
            margin-bottom: 10px;
        }

        /* --- セレクトボックス --- */
        select {
            width: 100%;
            padding: 12px 14px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            background-color: #ffffff;
            color: var(--text-main);
            font-size: 15px;
            outline: none;
            cursor: pointer;
            transition: border-color 0.2s, box-shadow 0.2s;
        }
        select:hover, select:focus {
            border-color: var(--primary-color);
            box-shadow: 0 0 0 3px rgba(29, 78, 216, 0.15);
        }

        /* --- ギガ数スライダー --- */
        .giga-wrapper {
            display: flex;
            align-items: center;
            gap: 20px;
        }
        .slider-box {
            flex-grow: 1;
        }
        input[type="range"] {
            -webkit-appearance: none;
            width: 100%;
            height: 8px;
            background: #d1d5db;
            border-radius: 4px;
            outline: none;
            cursor: pointer;
        }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 24px;
            height: 24px;
            background: var(--primary-color);
            border: 3px solid #ffffff;
            border-radius: 50%;
            box-shadow: 0 2px 6px rgba(0, 0, 0, 0.25);
            cursor: pointer;
            transition: transform 0.15s;
        }
        input[type="range"]::-webkit-slider-thumb:hover {
            transform: scale(1.1);
        }
        .slider-scale {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: var(--text-sub);
            margin-top: 8px;
        }
        .giga-badge {
            background-color: var(--primary-light);
            border: 2px solid var(--primary-color);
            min-width: 84px;
            padding: 10px 12px;
            border-radius: 10px;
            text-align: center;
        }
        .giga-badge .num {
            display: block;
            font-size: 24px;
            font-weight: 800;
            color: var(--primary-color);
            line-height: 1.1;
        }
        .giga-badge .unit {
            font-size: 12px;
            font-weight: 700;
            color: #1e3a8a;
        }

        /* --- チェックボックス（押しやすいカード型ボタン） --- */
        .checkbox-list {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
        }
        @media (max-width: 500px) {
            .checkbox-list {
                grid-template-columns: 1fr;
            }
        }
        .checkbox-label {
            display: flex;
            align-items: center;
            gap: 10px;
            background-color: #ffffff;
            border: 1px solid var(--border-color);
            padding: 12px 14px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            color: var(--text-main);
            cursor: pointer;
            transition: border-color 0.2s, background-color 0.2s;
        }
        .checkbox-label:hover {
            border-color: var(--primary-color);
            background-color: var(--primary-light);
        }
        .checkbox-label input {
            width: 18px;
            height: 18px;
            accent-color: var(--primary-color);
            cursor: pointer;
            margin: 0;
        }

        /* --- 検索実行ボタン --- */
        .submit-wrapper {
            text-align: center;
            margin: 8px 0 12px 0;
        }
        button.submit-btn {
            background-color: var(--primary-color);
            color: #ffffff;
            border: none;
            padding: 16px 48px;
            font-size: 16px;
            font-weight: 700;
            border-radius: 10px;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(29, 78, 216, 0.25);
            transition: background-color 0.2s, transform 0.15s;
            width: 100%;
            max-width: 340px;
        }
        button.submit-btn:hover {
            background-color: var(--primary-hover);
            transform: translateY(-1px);
        }

        /* --- 結果表示エリア --- */
        .results-section {
            display: none;
            margin-top: 36px;
            padding-top: 32px;
            border-top: 2px solid #e5e7eb;
        }
        .results-header {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 20px;
        }
        .results-header h2 {
            margin: 0;
            font-size: 19px;
            font-weight: 700;
            color: #111827;
        }
        .results-notice {
            font-size: 13px;
            color: var(--text-sub);
        }

        /* 結果カードの3列グリッド */
        .plan-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 18px;
        }
        @media (max-width: 780px) {
            .plan-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }
        @media (max-width: 500px) {
            .plan-grid {
                grid-template-columns: 1fr;
            }
        }

        /* プランカードデザイン */
        .plan-card {
            background-color: #ffffff;
            border: 1px solid #d1d5db;
            border-top-width: 5px;
            border-radius: 12px;
            padding: 20px 16px 16px 16px;
            text-decoration: none;
            color: inherit;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            box-shadow: var(--shadow-sm);
            transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;
        }
        .plan-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.08);
        }

        /* キャリア別のアクセントカラー（上部ボーダー） */
        .docomo { border-top-color: #cc0033; }
        .au { border-top-color: #eb5505; }
        .softbank { border-top-color: #6b7280; }
        .ahamo { border-top-color: #111827; }
        .uq { border-top-color: #e6005c; }
        .ymobile { border-top-color: #ff0033; }

        /* 第1位（最安プラン）の強調 */
        .plan-card:first-child {
            border-color: var(--primary-color);
            border-width: 2px;
            border-top-width: 6px;
            background-color: #f8faff;
            box-shadow: 0 6px 16px rgba(29, 78, 216, 0.12);
        }

        .card-top-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }
        .rank-badge {
            font-size: 12px;
            font-weight: 700;
            padding: 3px 10px;
            border-radius: 6px;
            background-color: #e5e7eb;
            color: #374151;
        }
        .plan-card:first-child .rank-badge {
            background-color: var(--primary-color);
            color: #ffffff;
        }
        .recommend-tag {
            font-size: 11px;
            font-weight: 700;
            color: #1e40af;
            background-color: #dbeafe;
            padding: 3px 8px;
            border-radius: 4px;
        }

        .plan-name {
            font-size: 16px;
            font-weight: 700;
            color: #111827;
            margin: 4px 0 8px 0;
            word-break: keep-all;
            overflow-wrap: break-word;
        }
        .plan-price {
            font-size: 26px;
            font-weight: 800;
            color: #111827;
            margin-bottom: 12px;
        }
        .plan-price span {
            font-size: 13px;
            font-weight: 600;
            color: var(--text-sub);
            margin-left: 2px;
        }
        .plan-card:first-child .plan-price {
            color: var(--primary-color);
        }

        .plan-detail {
            font-size: 12px;
            color: #374151;
            background-color: #f3f4f6;
            padding: 10px;
            border-radius: 8px;
            line-height: 1.5;
            margin-bottom: 14px;
        }
        .plan-card:first-child .plan-detail {
            background-color: #eff6ff;
        }

        .card-link-footer {
            font-size: 12px;
            font-weight: 700;
            color: var(--primary-color);
            text-align: right;
            border-top: 1px solid #e5e7eb;
            padding-top: 10px;
        }
        .plan-card:hover .card-link-footer {
            text-decoration: underline;
        }
    </style>
</head>
<body>

<div class="app-container">
    <header class="app-header">
        <h1>スマホ料金シミュレーター</h1>
        <p>毎月のデータ通信量や割引条件を選ぶだけで、最適な料金プランを安い順に比較できます</p>
    </header>
    
    <!-- 入力フォーム -->
    <div class="form-grid">
        <!-- 1. ギガ数選択 -->
        <div class="form-card full-width">
            <label class="form-label" for="giga">1. 1ヶ月に必要なデータ通信量</label>
            <div class="giga-wrapper">
                <div class="slider-box">
                    <input type="range" id="giga" min="1" max="120" value="20" oninput="updateGigaText(this.value)">
                    <div class="slider-scale">
                        <span>1GB</span>
                        <span>30GB</span>
                        <span>60GB</span>
                        <span>120GB</span>
                    </div>
                </div>
                <div class="giga-badge">
                    <span class="num" id="giga-val">20</span>
                    <span class="unit">GB</span>
                </div>
            </div>
        </div>
        
        <!-- 2. 年齢選択 -->
        <div class="form-card">
            <label class="form-label" for="age-limit">2. 契約者の年齢</label>
            <select id="age-limit">
                <option value="none">一般（30歳〜59歳）</option>
                <option value="u18">18歳以下</option>
                <option value="u22">19歳〜22歳</option>
                <option value="u29">23歳〜29歳</option>
                <option value="o60">60歳以上</option>
            </select>
        </div>
        
        <!-- 3. 通話オプション -->
        <div class="form-card">
            <label class="form-label" for="call">3. 通話オプション（かけ放題）</label>
            <select id="call">
                <option value="none">なし（30秒/22円）</option>
                <option value="5min">5分かけ放題</option>
                <option value="unlimited">24時間かけ放題</option>
            </select>
        </div>

        <!-- 4. 家族の回線数 -->
        <div class="form-card">
            <label class="form-label" for="family-lines">4. 家族の回線数</label>
            <select id="family-lines">
                <option value="1">1回線（家族割なし）</option>
                <option value="2">2回線</option>
                <option value="3">3回線以上</option>
            </select>
        </div>

        <!-- 5. クレジットカード決済 -->
        <div class="form-card">
            <label class="form-label" for="card-type">5. クレジットカード決済</label>
            <select id="card-type">
                <option value="none">なし</option>
                <option value="d-gold">dカード GOLD</option>
                <option value="d-silver">dカード SILVER</option>
                <option value="au-pay">au PAY カード</option>
                <option value="paypay-gold">PayPayカード GOLD</option>
                <option value="paypay-normal">PayPayカード（一般）</option>
            </select>
        </div>

        <!-- 6. セット割チェック -->
        <div class="form-card full-width">
            <span class="form-label">6. 適用可能なセット割引（複数選択可）</span>
            <div class="checkbox-list">
                <label class="checkbox-label">
                    <input type="checkbox" id="discount-net"> 自宅のネット回線セット割
                </label>
                <label class="checkbox-label">
                    <input type="checkbox" id="discount-denki"> でんき割
                </label>
            </div>
        </div>
    </div>
    
    <!-- 実行ボタン -->
    <div class="submit-wrapper">
        <button class="submit-btn" onclick="calculateRates()">料金をシミュレーションする</button>
    </div>
    
    <!-- 結果表示エリア -->
    <section class="results-section" id="results-area">
        <div class="results-header">
            <h2>おすすめプラン比較（月額・税込）</h2>
            <span class="results-notice">※各カードを選択すると公式サイトが開きます</span>
        </div>
        <div class="plan-grid" id="results-list"></div>
    </section>
</div>

<script>
    /* ====================================================
       📋 料金設定マスタ（将来の料金改定に対応しやすい設計）
       ==================================================== */
    const PLAN_CONFIG = {
        callOption: {
            standard: { 'none': 0, '5min': 880, 'unlimited': 1980 },
            included5min: { 'none': 0, '5min': 0, 'unlimited': 1100 }
        },
        urls: {
            docomo: 'https://www.docomo.ne.jp/',
            ahamo: 'https://ahamo.com/',
            au: 'https://www.au.com/',
            uq: 'https://www.uqwimax.jp/mobile/',
            softbank: 'https://www.softbank.jp/mobile/',
            ymobile: 'https://www.ymobile.jp/'
        }
    };

    function updateGigaText(val) {
        document.getElementById('giga-val').innerText = val;
    }

    function calculateRates() {
        const giga = parseInt(document.getElementById('giga').value, 10);
        const callOption = document.getElementById('call').value;
        const familyLines = parseInt(document.getElementById('family-lines').value, 10);
        const cardType = document.getElementById('card-type').value;
        const hasNet = document.getElementById('discount-net').checked;
        const hasDenki = document.getElementById('discount-denki').checked;
        const ageLimit = document.getElementById('age-limit').value;

        let results = [];

        /* ====================================================
           💡 1. ドコモ
           ==================================================== */
        let docomoPlanName = ''; let docomoBase = 0; let docomoDiscount = 0; let docomoRecommend = '';
        let docomoCall = PLAN_CONFIG.callOption.standard[callOption];

        if (giga <= 10) {
            docomoPlanName = 'ドコモmini'; docomoBase = (giga <= 4) ? 2750 : 3850;
            if (cardType === 'd-gold') docomoDiscount += 550;
            if (cardType === 'd-silver') docomoDiscount += 220;
            if (hasNet) docomoDiscount += 1210;
            if (hasDenki) docomoDiscount += 110;
            docomoRecommend = 'ライト利用向け';
        } else {
            if ((ageLimit === 'u18' || ageLimit === 'u22') && giga <= 30) {
                docomoPlanName = 'ドコモ U22特'; docomoBase = 6798;
                if (cardType === 'd-gold') docomoDiscount += 550;
                if (cardType === 'd-silver') docomoDiscount += 220;
                if (hasNet) docomoDiscount += 1210;
                if (familyLines >= 3) docomoDiscount += 1210;
                docomoDiscount += 3838;
                docomoRecommend = '若年層向け割引';
            } else {
                docomoPlanName = 'ドコモMAX'; docomoBase = 8448;
                if (cardType === 'd-gold') docomoDiscount += 550;
                if (cardType === 'd-silver') docomoDiscount += 220;
                if (hasNet) docomoDiscount += 1210;
                if (familyLines === 2) docomoDiscount += 550;
                if (familyLines >= 3) docomoDiscount += 1210;
                if (ageLimit === 'u29' || ageLimit === 'u18' || ageLimit === 'u22') {
                    docomoDiscount += 3838;
                    docomoRecommend = '期間限定特価';
                } else {
                    docomoRecommend = 'DAZN特典付き';
                }
            }
            if (hasDenki) docomoDiscount += 110;
        }
        let docomoFinalPrice = docomoBase - docomoDiscount + docomoCall;
        results.push({
            name: docomoPlanName,
            price: Math.max(0, docomoFinalPrice),
            class: 'docomo',
            detail: `基本料金: ${docomoBase.toLocaleString()}円<br>割引合計: -${docomoDiscount.toLocaleString()}円`,
            recommend: docomoRecommend,
            url: PLAN_CONFIG.urls.docomo
        });

        /* ====================================================
           💡 2. ahamo
           ==================================================== */
        let ahamoBase = (giga <= 30) ? 2970 : 4950;
        let ahamoCall = PLAN_CONFIG.callOption.included5min[callOption];
        results.push({
            name: 'ahamo',
            price: ahamoBase + ahamoCall,
            class: 'ahamo',
            detail: `5分通話無料込み<br>追加通話: +${ahamoCall.toLocaleString()}円`,
            recommend: 'シンプル設計',
            url: PLAN_CONFIG.urls.ahamo
        });

        /* ====================================================
           💡 3. au
           ==================================================== */
        let auPlanName = ''; let auBase = 0; let auDiscount = 0; let auCall = 0; let auRecommend = '';

        if (ageLimit === 'u18' && giga <= 20) {
            auPlanName = 'au U18プラン'; auBase = (giga <= 3) ? 2398 : 4048;
            if (cardType === 'au-pay') auDiscount += 220;
            if (hasNet) auDiscount += 550; 
            if (familyLines === 2) auDiscount += 220;
            if (familyLines >= 3) auDiscount += 550;  
            auCall = PLAN_CONFIG.callOption.standard[callOption];
            auRecommend = '18歳以下限定';
        } else if (ageLimit === 'o60' && giga <= 5) {
            auPlanName = 'au 60歳以上限定'; auBase = 4048;
            if (cardType === 'au-pay') auDiscount += 220;
            if (hasNet) auDiscount += 1100;
            auCall = PLAN_CONFIG.callOption.included5min[callOption];
            auRecommend = 'シニア向け5分無料';
        } else {
            auPlanName = 'au バリューリンク'; auBase = (giga <= 1) ? 6358 : 8008;
            if (cardType === 'au-pay') auDiscount += 220;
            if (hasNet) auDiscount += 1210;
            if (familyLines === 2) auDiscount += 660;
            if (familyLines >= 3) auDiscount += 1210;
            auCall = PLAN_CONFIG.callOption.standard[callOption];
            auRecommend = 'Pontaパス付き';
        }
        results.push({
            name: auPlanName,
            price: Math.max(0, auBase - auDiscount + auCall),
            class: 'au',
            detail: `基本料金: ${auBase.toLocaleString()}円<br>割引合計: -${auDiscount.toLocaleString()}円`,
            recommend: auRecommend,
            url: PLAN_CONFIG.urls.au
        });

        /* ====================================================
           💡 4. UQモバイル（ネット割・でんき割の重複回避ロジック）
           ==================================================== */
        if (giga <= 35) {
            let uqTokutokuBase = (giga <= 5) ? 2948 : 4048;
            let uqTokutokuDiscount = 0;
            let uqDiscountTypeLabel = 'なし';
            
            if (cardType === 'au-pay') uqTokutokuDiscount += 220;
            
            if (hasNet || hasDenki) { 
                uqTokutokuDiscount += 1100;
                if (hasNet && hasDenki) {
                    uqDiscountTypeLabel = 'セット割(重複回避)';
                } else if (hasNet) {
                    uqDiscountTypeLabel = '自宅セット割';
                } else {
                    uqDiscountTypeLabel = 'でんき割';
                }
            } else if (familyLines >= 2) { 
                uqTokutokuDiscount += 550; 
                uqDiscountTypeLabel = '家族割';
            }
            
            let uqTokutokuPrice = uqTokutokuBase - uqTokutokuDiscount + PLAN_CONFIG.callOption.standard[callOption];

            let uqComicomiBase = 3828;
            let uqComicomiCall = PLAN_CONFIG.callOption.included5min[callOption];
            let uqComicomiPrice = uqComicomiBase + uqComicomiCall;

            if (uqComicomiPrice < uqTokutokuPrice || giga > 30) {
                results.push({
                    name: 'UQコミコミバリュー',
                    price: uqComicomiPrice,
                    class: 'uq',
                    detail: `10分通話無料込み<br>月間35GBまで`,
                    recommend: '通話もデータもお得',
                    url: PLAN_CONFIG.urls.uq
                });
            } else {
                results.push({
                    name: 'UQトクトクプラン',
                    price: Math.max(0, uqTokutokuPrice),
                    class: 'uq',
                    detail: `基本料金: ${uqTokutokuBase.toLocaleString()}円<br>割引(${uqDiscountTypeLabel}): -${uqTokutokuDiscount.toLocaleString()}円`,
                    recommend: '高コスパ・中容量',
                    url: PLAN_CONFIG.urls.uq
                });
            }
        }

        /* ====================================================
           💡 5. ソフトバンク
           ==================================================== */
        let sbDiscount = 0;
        if (cardType === 'paypay-gold') sbDiscount += 550;
        if (cardType === 'paypay-normal') sbDiscount += 330;
        if (hasNet) sbDiscount += 1100;
        if (familyLines === 2) sbDiscount += 660;
        if (familyLines >= 3) sbDiscount += 1210;
        let sbCall = PLAN_CONFIG.callOption.standard[callOption];

        if (giga <= 5 && (5258 - sbDiscount + sbCall) < (8008 - sbDiscount + sbCall)) {
            results.push({
                name: 'SBミニフィット2',
                price: Math.max(0, 5258 - sbDiscount + sbCall),
                class: 'softbank',
                detail: `小容量向けプラン<br>割引合計: -${sbDiscount.toLocaleString()}円`,
                recommend: 'データ少なめ向け',
                url: PLAN_CONFIG.urls.softbank
            });
        } else {
            results.push({
                name: 'SBメリハリ無制限系',
                price: Math.max(0, 8008 - sbDiscount + sbCall),
                class: 'softbank',
                detail: `データ無制限<br>割引合計: -${sbDiscount.toLocaleString()}円`,
                recommend: 'データ使い放題',
                url: PLAN_CONFIG.urls.softbank
            });
        }

        /* ====================================================
           💡 6. Y!mobile
           ==================================================== */
        if (giga <= 35) {
            let ymBase = (giga <= 5) ? 3278 : ((giga <= 30) ? 4378 : 5478);
            let ymDiscount = 0;
            if (cardType === 'paypay-gold') ymDiscount += 770;
            if (cardType === 'paypay-normal') ymDiscount += 550;
            if (hasNet) {
                ymDiscount += 1650;
            } else if (familyLines >= 2) {
                ymDiscount += 1100;
            }
            let ymCall = PLAN_CONFIG.callOption.standard[callOption];
            results.push({
                name: 'Y!mobile',
                price: Math.max(0, ymBase - ymDiscount + ymCall),
                class: 'ymobile',
                detail: `基本料金: ${ymBase.toLocaleString()}円<br>割引合計: -${ymDiscount.toLocaleString()}円`,
                recommend: 'PayPay特典充実',
                url: PLAN_CONFIG.urls.ymobile
            });
        }

        // 料金の安い順にソート
        results.sort((a, b) => a.price - b.price);

        // カードとして描画
        const resultsList = document.getElementById('results-list');
        resultsList.innerHTML = '';

        results.forEach((item, index) => {
            const card = document.createElement('a');
            card.href = item.url;
            card.target = "_blank";
            card.rel = "noopener noreferrer";
            card.className = `plan-card ${item.class}`;
            
            const rankText = index === 0 ? '第 1 位（最安）' : `第 ${index + 1} 位`;

            card.innerHTML = `
                <div>
                    <div class="card-top-row">
                        <span class="rank-badge">${rankText}</span>
                        <span class="recommend-tag">${item.recommend}</span>
                    </div>
                    <div class="plan-name">${item.name}</div>
                    <div class="plan-price">¥${item.price.toLocaleString()}<span>/月</span></div>
                    <div class="plan-detail">${item.detail}</div>
                </div>
                <div class="card-link-footer">公式サイトで詳細を見る ↗</div>
            `;
            resultsList.appendChild(card);
        });

        const resultsArea = document.getElementById('results-area');
        resultsArea.style.display = 'block';
        resultsArea.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
    }
</script>

</body>
</html>