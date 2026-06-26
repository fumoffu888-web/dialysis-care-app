import streamlit as st
from datetime import datetime, timedelta

# 1. 網頁基礎設定 (優化行動裝置顯示)
st.set_page_config(page_title="慈濟腎友照護隨身助手 v2", page_icon="🩸", layout="centered")

# ==================== 自動彈窗提醒系統 ====================
# 偵測今天是否為每個月的第一個禮拜一或禮拜二
def check_blood_draw_day():
    today = datetime.now()
    # 找出當月1號
    first_day_of_month = today.replace(day=1)
    
    # 計算當月第一個禮拜一
    # weekday(): 0是禮拜一, 1是禮拜二...
    days_to_monday = (0 - first_day_of_month.weekday() + 7) % 7
    first_monday = first_day_of_month + timedelta(days=days_to_monday)
    
    # 計算當月第一個禮拜二
    days_to_tuesday = (1 - first_day_of_month.weekday() + 7) % 7
    first_tuesday = first_day_of_month + timedelta(days=days_to_tuesday)
    
    if today.date() == first_monday.date():
        return True, "今天是本月第一個【禮拜一】"
    elif today.date() == first_tuesday.date():
        return True, "今天是本月第一個【禮拜二】"
    return False, ""

is_blood_day, blood_day_msg = check_blood_draw_day()

if is_blood_day:
    # 使用 st.toast 或 st.error 進行強烈彈窗提醒
    st.error(f"📢 ⚠️ **【重要抽血提醒】** ⚠️ 📢\n\n{blood_day_msg}！**今天進行每個月的常規抽血檢驗**，請向護理站報到。醫師將依據報告評估您的透析狀態！")
    st.balloons() # 增加視覺動態吸引注意

# ==================== 主介面開始 ====================
st.title("🩸 血液透析腎友照護隨身助手 v2")
st.caption("📱 建議點選瀏覽器「加入主畫面」即可將本工具作為手機 APP 使用")

# 手機版導覽選單
menu = st.selectbox("功能選單", [
    "📌 報到須知與時間", 
    "🩺 雙腔導管照護與警示",
    "⚖️ 乾體重與水分計算", 
    "🥗 腎友飲食禁忌", 
    "🏃 透析運動須知", 
    "🚨 隨身電子救命卡"
])

# ==================== 模組一：報到須知與時間 ====================
if menu == "📌 報到須知與時間":
    st.header("🕒 本透析室血液透析時間")
    st.info("早班：07:25 開門 | 中班：12:20 開門 | 晚班：17:20 開門\n*(中/晚班前有清場消毒時間)*")
    
    st.subheader("💡 體重量測報到 4 步驟")
    st.markdown("""
    1. **插卡**：請先插入健保卡。
    2. **量測**：坐上體重機或站立於正中央。
    3. **離開**：聽到「嗶」聲後離開體重機。
    4. **取卡**：待測量體重出現方可取回健保卡。
    """)
    st.warning("⚠️ 每次透析前、後皆須插健保卡量體重，透析前須量體溫。未帶健保卡當日暫為最後排序。")

# ==================== 模組二：雙腔導管照護與警示 (新增) ====================
elif menu == "🩺 雙腔導管照護與警示":
    st.header("🩹 雙腔導管（暫時性洗腎通路）照護指南")
    st.write("請依據您身上放置導管的位置，查看對應的日常護理要點：")
    
    catheter_type = st.radio("選擇您的導管放置位置：", ["頸部 / 鎖骨下雙腔導管", "鼠溪部雙腔導管"])
    
    if catheter_type == "頸部 / 鎖骨下雙腔導管":
        st.subheader("★ 頸部導管照護要點")
        st.markdown("""
        * 🚫 **切勿拉扯**：勿用手拉扯導管，並將管路妥善固定於前胸區，避免滑脫。
        * 👕 **衣物選擇**：透析當日**不要穿著套頭衣物**，建議穿著寬鬆或前扣式上衣。
        * 💇 **長髮注意**：長髮者應將頭髮綁起；換藥時請戴上口罩。
        """)
    else:
        st.subheader("★ 鼠溪部導管照護要點")
        st.markdown("""
        * 🪑 **姿勢限制**：避免長時間保持 **90度坐姿** 或過度活動，以免導管彎曲或血液回流阻塞。
        * 🩳 **衣物選擇**：請穿著較寬鬆的褲子，避免摩擦、牽扯移位或管夾鬆開。
        """)
        
    st.markdown("---")
    st.subheader("🚨 異常狀況「紅腫熱痛」自我檢查清單")
    st.write("若您或家屬在日常觀察中發現以下任何一項症狀，請點選勾選：")
    
    # 交互式勾選清單
    symptom_1 = st.checkbox("傷口周圍出現 發紅、腫脹 跡象")
    symptom_2 = st.checkbox("傷口局部發熱 或 有明顯疼痛感")
    symptom_3 = st.checkbox("導管傷口處有 異常分泌物 或 滲血嚴重")
    symptom_4 = st.checkbox("病人出現 發燒、畏寒、呼吸短促、胸痛、咳嗽")
    symptom_5 = st.checkbox("💥 導管不慎滑脫、移位 或 縫線脫落")
    
    # 觸發警示判斷
    if symptom_5:
        st.error("🚨 嚴重緊急狀況：導管已脫落或移位！請立即以無菌紗布強力加壓傷口止血，並『盡速就醫』且立刻通知洗腎室！")
    elif symptom_1 or symptom_2 or symptom_3 or symptom_4:
        st.warning("⚠️ 警告：疑似發生感染或異常！請勿自行撕開防水透明膜。請盡快至門診就醫，或立刻打電話至透析室詢問！")
    else:
        st.success("🟢 目前檢查正常：請繼續保持皮膚周圍清潔乾燥。洗澡請採擦澡，切勿讓敷料潮濕。")

# ==================== 模組三：乾體重與水分計算 ====================
elif menu == "⚖️ 乾體重與水分計算":
    st.header("🧮 兩次透析間水分增重計算機")
    st.write("依據手冊標準：兩次透析間體重增加不可超過「乾體重的 5%」，且一天增加不超過 1.0~1.5 公斤。")
    
    dry_weight = st.number_input("1. 請輸入您的『乾體重』(kg):", min_value=30.0, max_value=150.0, value=60.0, step=0.1)
    current_weight = st.number_input("2. 請輸入今日透析前『實際體重』(kg):", min_value=30.0, max_value=150.0, value=62.5, step=0.1)
    
    max_allow_gain = round(dry_weight * 0.05, 2)
    actual_gain = round(current_weight - dry_weight, 2)
    
    st.markdown("---")
    st.subheader("📊 評估結果")
    st.metric(label="您的標準限制（乾體重 5%）", value=f"⚠️ 不可超過 {max_allow_gain} kg")
    
    if actual_gain > max_allow_gain:
        st.error(f"❌ 目前已增重 {actual_gain} kg！已超過安全標準。請特別注意水分控制，並配合醫師醫囑調整。")
    elif actual_gain < 0:
        st.warning(f"📉 目前體重低於乾體重 {abs(actual_gain)} kg。若有耳鳴、頭暈、抽筋，可能變胖了，請與醫師討論調高乾體重。")
    else:
        st.success(f"✅ 目前增重 {actual_gain} kg。控制得太棒了！請繼續保持。")

# ==================== 模組四：腎友飲食禁忌 ====================
elif menu == "🥗 腎友飲食禁忌":
    st.header("🚫 飲食三大紅線（低鉀、低磷、絕對禁楊桃）")
    tab1, tab2, tab3 = st.tabs(["❌ 絕對禁忌", "🥬 低鉀守則", "🥩 低磷與蛋白"])
    
    with tab1:
        st.error("🚨 楊桃：絕對不能吃！許多腎友食用後會一直打嗝，甚至昏迷。")
        st.warning("⚠️ 無鹽醬油：不可使用！因其鉀離子含量極高。")
    with tab2:
        st.subheader("高鉀水果與蔬菜限制")
        st.write("蔬菜烹調時**務必先川燙過**再炒，不喝湯汁。")
        st.markdown("**應少吃的高鉀水果：**")
        st.text("香蕉、棗子、芭樂、橘子、芒果、柿子、聖女番茄、哈密瓜、紅肉西瓜、奇異果。")
    with tab3:
        st.subheader("充分蛋白質與低磷")
        st.info("成人每日每公斤需 1.2-1.5 公克蛋白質，2/3 需為高生物價蛋白（如魚、肉、蛋、黃豆製品）。")
        st.markdown("**應少吃的高磷食物：**")
        st.text("全麥穀物、乳製品、核果類、動物內臟、蛋黃、豆類食品。")

# ==================== 模組五：透析運動須知 ====================
elif menu == "🏃 透析運動須知":
    st.header("🚴 穩定透析後的安全運動指南")
    col1, col2 = st.columns(2)
    with col1:
        st.success("🟢 適合做的運動")
        st.markdown("- 散步、走路、拉伸體操、瑜珈\n- 深蹲(蹲距)運動\n- 騎腳踏車、慢跑")
    with col2:
        st.error("🔴 出現以下狀況立即停止")
        st.markdown("- 有感冒症狀、發燒\n- 肌肉扭傷、挫傷或受傷\n- 腹痛、嘔吐\n- 血壓比平常高/低 20mmHg 以上")

# ==================== 模組六：隨身電子救命卡 ====================
elif menu == "🚨 隨身電子救命卡":
    st.header("🆘 隨身電子救命卡 (SOS)")
    name = st.text_input("患者姓名：", placeholder="請輸入姓名")
    blood_type = st.selectbox("血型：", ["A 型", "B 型", "O 型", "AB 型", "不確定"])
    fistula_arm = st.radio("洗腎通路位置（該處絕不可量血壓）：", ["左手廔管", "右手廔管", "雙腔導管"])
    contact = st.text_input("緊急聯絡人電話：", placeholder="例如：兒子 0912-xxxxxx")
    hospital = st.text_input("原洗腎院所：", value="台中慈濟醫院血液透析室")
    
    st.markdown("---")
    st.error(f"""
    📢 **我是血液透析 (洗腎) 患者**
    * **姓名**：{name} | **血型**：{blood_type}
    * **廔管禁忌**：⚠️ 【{fistula_arm}】有洗腎通路，**該手禁量血壓、禁抽血打針**！
    * **緊急聯絡人**：{contact}
    * **原透析醫院**：{hospital}
    
    🚨 **若我意識不清，請幫我撥打 119 聯絡救護車送醫！**
    """)
