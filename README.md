import customtkinter as ctk  
from PIL import Image
import requests
from io import BytesIO
from datetime import datetime

# --- GELECEK TEMASI AYARLARI ---
ctk.set_appearance_mode("dark") 
NEON_BLUE = "#00d4ff"
NEON_PURPLE = "#b026ff"
NEON_GREEN = "#39ff14"
BG_DARK = "#0a0b10"
PANEL_COLOR = "#12121f"

class FutureInsaatOS(ctk.CTk):
    def __init__(self):
        super().__init__()

        self.title("NEURAL-CONSTRUCT OS v9.0 - 2100 Edition")
        self.geometry("1200x850")
        
        self.usd_try = self.kur_cek()
        
        try: # bak
            img_url = "https://images.unsplash.com/photo-1614850523296-d8c1af93d400?q=80&w=2000"
            response = requests.get(img_url)
            self.bg_image = ctk.CTkImage(Image.open(BytesIO(response.content)), size=(1200, 850))
            self.bg_label = ctk.CTkLabel(self, image=self.bg_image, text="")
            self.bg_label.place(x=0, y=0)
        except:
            self.configure(fg_color=BG_DARK)

        self.main_container = ctk.CTkFrame(self, corner_radius=15, fg_color="#0f0f19", border_width=2, border_color=NEON_BLUE)
        self.main_container.place(relx=0.5, rely=0.5, anchor="center", relwidth=0.9, relheight=0.9)

        self.sidebar = ctk.CTkFrame(self.main_container, corner_radius=10, fg_color="transparent", width=280)
        self.sidebar.pack(side="left", fill="y", padx=20, pady=20)

        self.logo = ctk.CTkLabel(self.sidebar, text="NEURAL-LINK\nCONSTRUCT", font=("Courier New", 28, "bold"), text_color=NEON_BLUE)
        self.logo.pack(pady=40)

        self.create_nav_btn("📊 DAİRE ANALİZİ", self.show_daire)
        self.create_nav_btn("🏰 VİLLA KOMPLEKSİ", self.show_villa)
        self.create_nav_btn("🏙️ MEGA YAPI", self.show_apartman)
        
        self.live_feed = ctk.CTkLabel(self.sidebar, text=f"USD/TRY: {self.usd_try:.2f}\nSTAT: ONLINE", font=("Consolas", 12), text_color=NEON_GREEN)
        self.live_feed.pack(side="bottom", pady=20)

        self.display = ctk.CTkFrame(self.main_container, corner_radius=15, fg_color=PANEL_COLOR)
        self.display.pack(side="right", fill="both", expand=True, padx=20, pady=20)

        self.show_welcome()

    def kur_cek(self):
        try: return requests.get("https://open.er-api.com/v6/latest/USD").json()["rates"]["TRY"]
        except: return 34.45

    def create_nav_btn(self, text, command):
        btn = ctk.CTkButton(self.sidebar, text=text, font=("Arial", 14, "bold"), height=55, fg_color="transparent", border_width=1, border_color=NEON_BLUE, hover_color="#1a1a2e", anchor="w", command=command)
        btn.pack(pady=10, padx=10, fill="x")

    def show_welcome(self):
        for w in self.display.winfo_children(): w.destroy()
        msg = ctk.CTkLabel(self.display, text="SİSTEME ERİŞİM SAĞLANDI...\nLÜTFEN BİR PROJE TARAMASI BAŞLATIN", font=("Consolas", 20), text_color=NEON_BLUE)
        msg.pack(expand=True)

    def add_input_row(self, parent, label):
        frame = ctk.CTkFrame(parent, fg_color="transparent")
        frame.pack(fill="x", pady=8)
        lbl = ctk.CTkLabel(frame, text=label, font=("Arial", 13), text_color="white")
        lbl.pack(side="left", padx=10)
        ent = ctk.CTkEntry(frame, border_color=NEON_PURPLE, fg_color="#1a1a2e", width=140)
        ent.pack(side="right", padx=10)
        return ent

    # --- DAİRE MODÜLÜ ---
    def show_daire(self):
        for w in self.display.winfo_children(): w.destroy()
        scroll = ctk.CTkScrollableFrame(self.display, fg_color="transparent")
        scroll.pack(fill="both", expand=True, padx=10, pady=10)

        inputs = {
            "Mutfak": self.add_input_row(scroll, "Mutfak m²"),
            "Banyo": self.add_input_row(scroll, "Banyo m²"),
            "Balkon": self.add_input_row(scroll, "Balkon m²")
        }

        oda_listesi = []
        def oda_ekle():
            row = ctk.CTkFrame(scroll, fg_color="transparent")
            row.pack(fill="x", pady=5)
            e_ad = ctk.CTkEntry(row, placeholder_text="Oda Adı", width=120)
            e_ad.pack(side="left", padx=5)
            e_m2 = ctk.CTkEntry(row, placeholder_text="m²", width=80)
            e_m2.pack(side="left", padx=5)
            oda_listesi.append((e_ad, e_m2))

        ctk.CTkButton(scroll, text="+ NÖRAL ODA EKLE", fg_color=NEON_PURPLE, command=oda_ekle).pack(pady=15)
        
        kalite = ctk.CTkSegmentedButton(scroll, values=["Ekonomik", "Standart", "Nano-Premium"])
        kalite.set("Standart")
        kalite.pack(pady=10)

        def hesapla():
            try:
                m2 = sum(float(x.get() or 0) for x in inputs.values())
                m2 += sum(float(x[1].get() or 0) for x in oda_listesi)
                carpan = {"Ekonomik": 0.8, "Standart": 1.0, "Nano-Premium": 1.8}[kalite.get()]
                self.final_rapor(m2 * 1100 * carpan)
            except: pass

        ctk.CTkButton(self.display, text="ANALİZİ BAŞLAT", fg_color=NEON_GREEN, text_color="black", font=("Arial", 16, "bold"), height=50, command=hesapla).pack(pady=20, padx=40, fill="x")

    # --- GELİŞTİRİLMİŞ VİLLA MODÜLÜ ---
    def show_villa(self):
        for w in self.display.winfo_children(): w.destroy()
        scroll = ctk.CTkScrollableFrame(self.display, fg_color="transparent", label_text="VİLLA YAPILANDIRMASI")
        scroll.pack(fill="both", expand=True, padx=10, pady=10)

        # Temel Bilgiler
        katsayi = self.add_input_row(scroll, "Kat Sayısı")
        bahce_m2 = self.add_input_row(scroll, "Peyzaj / Bahçe Alanı (m²)")
        
        # Sabit Oda Detayları
        inputs = {
            "Salon (Grand Hall)": self.add_input_row(scroll, "Salon m²"),
            "Mutfak (Smart Kitchen)": self.add_input_row(scroll, "Mutfak m²"),
            "Ebeveyn Banyo": self.add_input_row(scroll, "Ana Banyo m²"),
            "Misafir Banyo": self.add_input_row(scroll, "Misafir Lavabo m²")
        }

        # Dinamik Oda Ekleme (Yatak Odaları, Sinema, vb.)
        villa_odalar = []
        def villa_oda_ekle():
            row = ctk.CTkFrame(scroll, fg_color="transparent")
            row.pack(fill="x", pady=5)
            e_ad = ctk.CTkEntry(row, placeholder_text="Bölüm Adı (Örn: Sinema)", width=150)
            e_ad.pack(side="left", padx=5)
            e_m2 = ctk.CTkEntry(row, placeholder_text="m²", width=80)
            e_m2.pack(side="left", padx=5)
            villa_odalar.append((e_ad, e_m2))

        ctk.CTkButton(scroll, text="+ ÖZEL YAŞAM MODÜLÜ EKLE", fg_color=NEON_PURPLE, command=villa_oda_ekle).pack(pady=15)

        # Donanımlar
        havuz = ctk.CTkSwitch(scroll, text="Anti-Gravite Havuz Modülü", progress_color=NEON_BLUE)
        havuz.pack(pady=10)
        garaj = ctk.CTkSwitch(scroll, text="Tesla-Hangarı (Otopark)", progress_color=NEON_BLUE)
        garaj.pack(pady=10)

        def hesapla():
            try:
                # Toplam kapalı alan hesabı
                kapali_alan = sum(float(x.get() or 0) for x in inputs.values())
                kapali_alan += sum(float(x[1].get() or 0) for x in villa_odalar)
                
                # Kat sayısı ile çarpım (Genellikle toplam metrekare girilir ama kat başı ortalama için)
                toplam_kapali = kapali_alan * float(katsayi.get() or 1)
                bahce = float(bahce_m2.get() or 0)
                
                # Villa Birim Maliyeti (2100 yılı lüks standartları)
                maliyet = (toplam_kapali * 1600) + (bahce * 350)
                
                if havuz.get(): maliyet += 35000
                if garaj.get(): maliyet += 20000
                
                self.final_rapor(maliyet)
            except: pass

        ctk.CTkButton(self.display, text="VİLLA ANALİZİNİ ÇALIŞTIR", fg_color=NEON_BLUE, text_color="white", font=("Arial", 16, "bold"), height=50, command=hesapla).pack(pady=20, padx=40, fill="x")

    # --- APARTMAN MODÜLÜ ---
    def show_apartman(self):
        for w in self.display.winfo_children(): w.destroy()
        kat = self.add_input_row(self.display, "Kat Sayısı")
        daire_kat = self.add_input_row(self.display, "Kattaki Daire Sayısı")
        m2_daire = self.add_input_row(self.display, "Standart Daire m²")
        asansor = self.add_input_row(self.display, "Maglev Asansör Sayısı")

        def hesapla():
            try:
                m2 = float(kat.get() or 0) * float(daire_kat.get() or 0) * float(m2_daire.get() or 0)
                maliyet = (m2 * 950) + (float(asansor.get() or 0) * 15000)
                self.final_rapor(maliyet)
            except: pass

        ctk.CTkButton(self.display, text="MEGA YAPI ANALİZİ", fg_color=NEON_PURPLE, command=hesapla).pack(pady=20, padx=40, fill="x")

    def final_rapor(self, usd_maliyet):
        tl = usd_maliyet * self.usd_try
        report = ctk.CTkToplevel(self)
        report.title("DATA REPORT")
        report.geometry("450x550")
        report.configure(fg_color="#05050a")
        report.attributes("-topmost", True)

        ctk.CTkLabel(report, text="--- MALİYET PROJEKSİYONU ---", text_color=NEON_GREEN, font=("Consolas", 18)).pack(pady=20)
        info = f"TARİH: {datetime.now().strftime('%Y-%m-%d')}\n\nTOPLAM (USD): ${usd_maliyet:,.2f}\nTOPLAM (TRY): {tl:,.2f} ₺\n\nAnaliz: GÜNCEL\nBirim Maliyet: 2100 Standart\nİşçilik (Robotik): DAHİL"

        box = ctk.CTkTextbox(report, width=400, height=280, font=("Consolas", 14), fg_color="#101020", text_color=NEON_BLUE)
        box.insert("0.0", info)
        box.pack(pady=10)

        def dosya_kaydet():
            with open("Proje_Raporu_2100.txt", "w", encoding="utf-8") as f: f.write(info)
            btn_save.configure(text="ARŞİVLENDİ!", fg_color=NEON_GREEN)

        btn_save = ctk.CTkButton(report, text="VERİYİ KAYDET", command=dosya_kaydet, fg_color="#333333")
        btn_save.pack(pady=20)

if __name__ == "__main__":
    app = FutureInsaatOS()
    app.mainloop()





