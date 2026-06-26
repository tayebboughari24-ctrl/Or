import streamlit as st
import pandas as pd
import json
import os
from datetime import datetime

# إعدادات الصفحة
st.set_page_config(
    page_title="مجوهرات النخبة - Elite Jewelry",
    page_icon="👑",
    layout="wide"
)

# تخصيص المظهر بألوان تناسب محلات الذهب
st.markdown("""
    <style>
    .main { background-color: #fcfbfa; }
    h1, h2, h3 { color: #1a2a3a; font-family: 'Cairo', sans-serif; }
    .gold-text { color: #c5a059; font-weight: bold; }
    </style>
""", unsafe_allow_html=True)

DATA_FILE = "gold_products.json"

# دالة تحميل البيانات
def load_products():
    if os.path.exists(DATA_FILE):
        try:
            with open(DATA_FILE, "r", encoding="utf-8") as f:
                return json.load(f)
        except:
            return []
    return []

# دالة حفظ البيانات
def save_products(products):
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(products, f, ensure_ascii=False, indent=4)

if 'products' not in st.session_state:
    st.session_state.products = load_products()

# القائمة الجانبية
st.sidebar.title("👑 مجوهرات النخبة")
page = st.sidebar.radio("انتقل إلى:", ["الرئيسية ومتجر الذهب", "حاسبة الادخار الذكية", "لوحة تحكم الإدارة 🔐"])

# ==========================================
# 1. صفحة العرض والطلب للزبائن (للقراءة فقط)
# ==========================================
if page == "الرئيسية ومتجر الذهب":
    st.title("✨ أحدث التشكيلات والمجوهرات الفاخرة")
    st.write("تصفح مجموعتنا الفريدة واستفسر أو احجز مباشرة عبر الواتساب.")
    
    products = st.session_state.products
    
    if not products:
        st.info("لا توجد قطع معروضة حالياً.")
    else:
        cols = st.columns(3)
        for idx, prod in enumerate(products):
            with cols[idx % 3]:
                st.markdown(f"### {prod['name']}")
                
                # عرض الصورة من خلال الرابط المباشر المرفوع على الإنترنت
                st.image(prod['image_url'], use_column_width=True)
                
                st.markdown(f"**العيار:** <span class='gold-text'>عيار {prod['caliber']}</span>", unsafe_allow_html=True)
                st.write(f"**الوزن:** {prod['weight']} جرام")
                st.write(f"**السعر:** {prod['price']} د.ج")
                
                # إرسال التفاصيل مباشرة للواتساب الخاص بالمحل
                whatsapp_number = "+213775698325" 
                message = f"مرحباً، أود الاستفسار عن قطعة الذهب: {prod['name']}\nالعيار: {prod['caliber']}\nالوزن: {prod['weight']} جرام"
                whatsapp_url = f"https://wa.me/{whatsapp_number}?text={message.replace(' ', '%20').replace('\n', '%0A')}"
                
                st.markdown(f'<a href="{whatsapp_url}" target="_blank"><button style="width:100%; background-color:#25D366; color:white; border:none; padding:8px; border-radius:5px; cursor:pointer; font-weight:bold;">📱 اطلب عبر واتساب</button></a>', unsafe_allow_html=True)
                st.markdown("---")

# ==========================================
# 2. حاسبة الادخار
# ==========================================
elif page == "حاسبة الادخار الذكية":
    st.title("📊 حاسبة ادخار الذهب")
    gold_price_21 = st.number_input("سعر غرام الذهب عيار 21 اليوم (د.ج):", value=11000)
    budget = st.number_input("أدخل المبلغ المراد استثماره (د.ج):", min_value=0, value=50000)
    
    if budget > 0:
        weight_21 = budget / gold_price_21
        st.success(f"يمكنك شراء: {weight_21:.2f} جرام من عيار 21")

# ==========================================
# 3. لوحة تحكم الإدارة (محمية بكلمة مرور)
# ==========================================
elif page == "لوحة تحكم الإدارة 🔐":
    st.title("🔐 لوحة التحكم (خاصة بصاحب المحل)")
    password = st.text_input("أدخل كلمة مرور الإدارة:", type="password")
    
    if password == "admin123":
        st.success("تم تسجيل الدخول بنجاح.")
        
        with st.form("add_gold_form", clear_on_submit=True):
            prod_name = st.text_input("اسم قطعة الذهب:")
            caliber = st.selectbox("العيار:", [21, 18, 24])
            weight = st.number_input("الوزن (جرام):", min_value=0.0, step=0.1)
            price = st.number_input("السعر (د.ج):", min_value=0)
            
            # هنا نضع رابط الصورة المباشر ليتوافق مع السيرفر و جيت هاب
            image_url = st.text_input("رابط صورة القطعة المباشر (URL):", placeholder="https://example.com/image.jpg")
            
            submit = st.form_submit_button("🚀 نشر القطعة")
            
            if submit and prod_name and image_url:
                new_prod = {
                    "name": prod_name,
                    "caliber": caliber,
                    "weight": weight,
                    "price": price,
                    "image_url": image_url,
                    "date": datetime.now().strftime("%Y-%m-%d")
                }
                st.session_state.products.append(new_prod)
                save_products(st.session_state.products)
                st.success("تم النشر بنجاح!")
                st.rerun()

        # قسم الحذف
        st.subheader("🗑️ إدارة المعرض")
        if st.session_state.products:
            df = pd.DataFrame(st.session_state.products)
            st.dataframe(df[["name", "caliber", "weight", "price"]])
            delete_idx = st.number_input("رقم القطعة لحذفها:", min_value=0, max_value=len(st.session_state.products)-1, step=1)
            if st.button("❌ حذف"):
                st.session_state.products.pop(delete_idx)
                save_products(st.session_state.products)
                st.success("تم الحذف.")
                st.rerun()
