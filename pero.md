#### Prikaz števila registriranih osebnih vozil po znamkah

```python
podatkiOsebnaVozila = podatki[podatki["KATEGORIJA_OPIS"] == "osebni avtomobil"]
countZnamkeOsebnihVozil = podatkiOsebnaVozila.groupby("ZNAMKA")["ZNAMKA"].count()

dictCount = countZnamkeOsebnihVozil.to_dict()

# obdržimo samo znamke ki imajo vsaj 50 vnosov
for key in list(dictCount.keys()):
    if(dictCount[key] < 50):
        del dictCount[key]

keysCount = dictCount.keys()
valuesCount = list(dictCount.values())

k = list()

for key in keysCount:
    k.append(str(key) + " (" + str(dictCount[key]) + ")" )
    
fig = plt.figure(figsize=(14,8))
plt.bar(k, valuesCount)
plt.xticks(rotation = 90)
plt.xlabel("Znamke")
plt.ylabel("Število osebnih avtomobilov")
plt.title("Število osebnih avtomobilov glede na Znamko")
plt.show
```

Kot je iz grafa razvidno imamo največ osebnih vozil znamke Renault in nato Volkswagen. Za prvimi dvemi pa imamo še znamke Opel, Peugeot in Citroën. V grafu sem upošteval samo znamke ki imajo vsaj 50 vnosov, ker imamo veliko znamk s pod 50timi vnosi in bi težko razbrali podatke iz grafa.


#### Odstotek brezhibno opravljenih Tehničnih pregledov osebnih avtomobilov po znamkah

```python
podatkiOsebnaVozila = podatki[podatki["KATEGORIJA_OPIS"] == "osebni avtomobil"]
p1 = podatkiOsebnaVozila[podatkiOsebnaVozila["TEHNICNI_PREGLED_STATUS"] == "brezhiben"].groupby("ZNAMKA")["ZNAMKA"].count()

p2 = podatkiOsebnaVozila.groupby("ZNAMKA")["ZNAMKA"].count()
p2 = p2.to_dict()

for key in list(p2.keys()):
    if(p2[key] < 50):
        del p2[key]

brezhib = dict()
for key in p2:
    brezhib[key] = p1[key] / p2[key] *100  

#deset najbolj brezhibnih znamk
b = pd.DataFrame.from_dict(brezhib, orient="index")
b.columns = ["Odstotek brezhibnih"]
b.nlargest(10, "Odstotek brezhibnih")
```

**Deset najbolj brezhibnih znamk osebnih avtomobilov**

|Znamka|Odstotek brezhibnosti|
|------|---------------------|
|CIMOS|92.59|
|ZASTAVA|89.48|
|PORSCHE|84.86|
|FIAT-ADRIA|82.06|
|JAGUAR|81.98|
|IMV|81.35|
|LEXUS|80.88|
|MERCEDES BENZ|80.57|
|DAIHATSU|80.53|
|LAND ROVER|80.49|

Poizvedba 10 najbolj breizhibnih znamk osebnih avtomobilov. Odstotek breizhibnosti je pa je odstotek brizhibno opravljenih tehničnih pregledov vsake znamke. Upoštevali smo le znamke ki imajo vsaj 50 vnosov.