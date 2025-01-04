# TriAldi
UAS Kecerdasan Buatan
!pip install -U scikit-fuzzy


import numpy as np
import skfuzzy as fuzz
import skfuzzy.control as ctrl
import matplotlib.pyplot as plt

# 1. Definisi Variabel Fuzzy
suhu = ctrl.Antecedent(np.arange(16, 36, 1), 'suhu')
kelembapan = ctrl.Antecedent(np.arange(20, 81, 1), 'kelembapan')
kipas = ctrl.Consequent(np.arange(0, 101, 1), 'kipas')

# 2. Definisi Himpunan Fuzzy
suhu['dingin'] = fuzz.trimf(suhu.universe, [16, 16, 25])
suhu['normal'] = fuzz.trimf(suhu.universe, [20, 25, 30])
suhu['panas'] = fuzz.trimf(suhu.universe, [25, 35, 35])

kelembapan['rendah'] = fuzz.trimf(kelembapan.universe, [20, 20, 50])
kelembapan['sedang'] = fuzz.trimf(kelembapan.universe, [30, 50, 70])
kelembapan['tinggi'] = fuzz.trimf(kelembapan.universe, [50, 80, 80])

kipas['lambat'] = fuzz.trimf(kipas.universe, [0, 0, 50])
kipas['sedang'] = fuzz.trimf(kipas.universe, [20, 50, 80])
kipas['cepat'] = fuzz.trimf(kipas.universe, [50, 100, 100])

# 3. Aturan Fuzzy
rule1 = ctrl.Rule(suhu['dingin'] & kelembapan['rendah'], kipas['lambat'])
rule2 = ctrl.Rule(suhu['dingin'] & kelembapan['sedang'], kipas['lambat'])
rule3 = ctrl.Rule(suhu['dingin'] & kelembapan['tinggi'], kipas['sedang'])
rule4 = ctrl.Rule(suhu['normal'] & kelembapan['rendah'], kipas['lambat'])
rule5 = ctrl.Rule(suhu['normal'] & kelembapan['sedang'], kipas['sedang'])
rule6 = ctrl.Rule(suhu['normal'] & kelembapan['tinggi'], kipas['cepat'])
rule7 = ctrl.Rule(suhu['panas'] & kelembapan['rendah'], kipas['sedang'])
rule8 = ctrl.Rule(suhu['panas'] & kelembapan['sedang'], kipas['cepat'])
rule9 = ctrl.Rule(suhu['panas'] & kelembapan['tinggi'], kipas['cepat'])

# 4. Sistem Kontrol
kipas_ctrl = ctrl.ControlSystem([rule1, rule2, rule3, rule4, rule5, rule6, rule7, rule8, rule9])
kipas_simulasi = ctrl.ControlSystemSimulation(kipas_ctrl)

# 5. Simulasi Input & Output
input_suhu = 28  # Ubah nilai suhu untuk pengujian
input_kelembapan = 65  # Ubah nilai kelembapan untuk pengujian

kipas_simulasi.input['suhu'] = input_suhu
kipas_simulasi.input['kelembapan'] = input_kelembapan
kipas_simulasi.compute()

output_kipas = kipas_simulasi.output['kipas']
print(f"Suhu: {input_suhu}°C, Kelembapan: {input_kelembapan}% -> Kecepatan Kipas: {output_kipas:.2f}%")

# 6. Visualisasi Fungsi Keanggotaan
suhu.view()
kelembapan.view()
kipas.view()
plt.show()
