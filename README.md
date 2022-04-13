# PR21gpnfmt #

## TEHNIČNI PREGLEDI MOTORNIH VOZIL

### Avtorji ###
Nejc Frank, Gašper Perovič, Matija Tomažič

## Vmesno poročilo o opravljenem delu 


### 1. Predstavitev naše množice podatkov

Množica podatkov nad katero bomo opravljali raziskave in ugotavljali kakšne vplive imajo določeni atributi na druge, smo našli na spletni strani www.podatki.gov.si. Na omenjeni spletni strani so različni statistični podatki zbrani iz Slovenije, kar je tudi razlog, da smo se odločili uporabljati te podatke oz. množico podatkov.

Množica je sestavljena iz podatkov, ki opisujejo vozilo (znamka,tovarniška oznaka, vrsta goriva, prevoženi kilometri...) ter podatkov, ki nam povedo, če je vozilo tehnično dovršeno in kje opravlja tehnične preglede.

Za to množico podatkov smo se odločili, ker se nam je zdela zanimiva za ugotvaljanje, opravljanja tehničnih pregledov glede na znamko, leto proizvodnje, vrsto goriva, pri koliko kilometrov različna vozila niso več tehnično brezhibna ipd.

Pri izbiri množice smo bili pozorni tudi na to, da vsebuje dovolj podatkov za obdelavo saj nismo hoteli biti omejeni pri naših raziskavah.


### 2. Manjšanje množice podatkov 

Po odločitvi o izbiri podatkov smo pri uvozu podatkov naleteli na težavo, saj je bila množica podatkov prevelika. Zaradi zahteve po dovolj veliki množici se je izkazalo, da je bilo podatkov preveč za uvoz na git, zato smo množico skrajšali iz pogleda števila posameznih avtov ne pa iz pogleda števila podatkov posameznega vozila.


### 3. Branje podatkov

Množica podatkov, ki smo jo prenesli iz spletne strani je bila v obliki csv datoteke. V DataFrame smo jo uvozili preko knjižnice "PANDAS", da smo jo lahko v nadaljevanju klicali po stolpcih, ki smo jih uporabili za različne poizvedbe.

Branje datoteke:

```python
import pandas as pd 
podatki = pd.read_csv("Porocilo_o_uspesnosti_TP_1.csv", delimiter=";", low_memory=False)
```


### 4. Opis atributov in vrednosti

- Znamka - diskretni atribut
- Tovarniška oznaka - diskretni atribut
- Komercialna oznaka - diskretni atribut
- Komercialni tip - diskretni atribut
- Kategorija oznaka - diskretni atribut
- Kategorija opis - diskretni atribut
- Nadgradnja oznaka - diskretni atribut
- Nadgradnja opis - diskretni atribut
- Vrsta goriva - zvezni atribut
- Datum prve registracije in nato v SLO - zvezna atributa
- Prevoženi kilometri - zvezni atribut
- Lastnik vozila (P ali F) - pravna ali fizična oseba - diskretni atribut
- Tehnični pregled status - diskretni atribut
- Veljavnost tehničnega pregleda od-do - zvezni atribut
- Izvajalna enota - diskretni atribut


### 5. Rezultati

#### - Poizvedba, prikaže število motornih vozil glede na vrsto vozila, izpisano z stolpičnim diagramom. 

```python
znamka = podatki[['KATEGORIJA_OPIS']]
znamka = podatki.groupby('KATEGORIJA_OPIS')['ZNAMKA'].count()
znamka1 = znamka.to_dict()

k = list()
for key in znamka1:
    k.append(str(key) + " (" + str(znamka1[key]) + ")" )
    
    
#znamka1.pop('osebni avtomobil')
fig = plt.figure(figsize=(25,8))
plt.bar(k,(znamka1.values()), width=0.5)
plt.xticks(rotation=45)
plt.title("Število vozil glede na določeno kategorijo", size="30")
plt.show()
```

![slika](https://user-images.githubusercontent.com/100125468/163144125-0090c3c8-fb4c-4a5e-b449-75eaeafe186c.png)

Ker je bilo število osebnih avtomobilov naprimerno večje kot vsi ostali rezultati smo zaradi preglednosti izločili podatke od osebnih vozilih in izpisali le vsa ostala motorna vozila.

<br></br>
#### - Poizvedba, ki prikazuje 10 izvajalnih enot, pri katerih je bilo največ vozil tehnično brezhibnih.

```python
narejeno1 = podatki['TEHNICNI_PREGLED_STATUS'].tolist()
narejeno2 = podatki['IZVAJALNA_ENOTA_OPIS'].tolist()
narejeno = podatki[podatki["TEHNICNI_PREGLED_STATUS"] == "brezhiben"].groupby("IZVAJALNA_ENOTA_OPIS")["IZVAJALNA_ENOTA_OPIS"].count()
narejeno = narejeno.to_dict()

dict = {'Name':narejeno.keys(), 'value':(narejeno.values())}
df = pd.DataFrame(dict)
df.nlargest(10,"value")
```

![slika](https://user-images.githubusercontent.com/100125468/163145287-3d24863f-dd8d-4128-8ced-dff00f19e5b2.png)

#### - Prikaz števila registriranih osebnih vozil po znamkah

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
![image](https://user-images.githubusercontent.com/64787664/163146822-6ecdb6b3-87fc-4f04-b17e-0b6d7cb3a3af.png)

Kot je iz grafa razvidno imamo največ osebnih vozil znamke Renault in nato Volkswagen. Za prvimi dvemi pa imamo še znamke Opel, Peugeot in Citroën. V grafu sem upošteval samo znamke ki imajo vsaj 50 vnosov, ker imamo veliko znamk s pod 50-imi vnosi in bi težko razbrali podatke iz grafa.


#### - Odstotek brezhibno opravljenih Tehničnih pregledov osebnih avtomobilov po znamkah

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

![image](https://user-images.githubusercontent.com/64787664/163146975-d33b70fc-6fef-449c-83a1-7f08b21e80d3.png)

Poizvedba 10 najbolj breizhibnih znamk osebnih avtomobilov. Odstotek breizhibnosti je pa je odstotek brizhibno opravljenih tehničnih pregledov vsake znamke. Upoštevali smo le znamke ki imajo vsaj 50 vnosov.




