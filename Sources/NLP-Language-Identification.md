# Swift - NLP -  Identification de la langue

## 1. Langue dominante

La première étape du processus va consister à identifier la langue utilisée dans le texte.

L'identification linguistique [^1]  est la tâche de détecter automatiquement la langue et l'écriture d'un morceau de texte. En langage naturel, **NLLanguageRecognizer** [^2] effectue cette tâche.

À l'aide d'un outil de reconnaissance de langue, vous pouvez obtenir la langue la plus probable pour un morceau de texte d'entrée, ou un ensemble de candidats de langue possibles avec leurs probabilités associées. Vous pouvez également contraindre le processus d'identification en fournissant une liste d'indices sur les probabilités connues pour les langues, ou une liste de langues contre lesquelles les prédictions sont limitées. Vous trouverez des langues prises en charge dans **NLLanguage** [^3],  mais vous pouvez aussi définir et utiliser vos propres balises de langue.

Pour configurer un « recognizer » il faut créer une instance de **NLLanguageRecognizer** [^4] et d’appeler sa méthode **processString(**_:) ._ [^5]

Par exemple : 

```swift
func recognizeLanguage(_ text: String) {
    let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    recognizer.processString(text)
}

```

Ici le recognizer est défini, on peut interroger la propriété dominantLanguage pour évaluer la langue dominante du texte, prenons par exemple, six phrases, une en français, une en anglais,  une en russe, une en japonais, nue en grec,  une en chinois traditionnel et une en hébreu :

```swift
let textFR = "Il était une fois,  dans un royaume fort fort lointain"
let textEN = "Once upon a time, in a kingdom, far far away"
let textRU = "Давным-давно, в далёком-далеком королевстве"
let textJP = "むかしむかし、遠く離れた王国で"
let textGR = "Μια φορά κι έναν καιρό, σε ένα βασίλειο μακρινό"
let textCN = "很久很久以前，在一個遙遠的王國里"
let textHE = "פעם, בממלכה רחוקה רחוקה"
```

Nous allons maintenant créer la méthode qui va permettre de retourner la langue dominante sous la forme d’un paramètre de type NLLanguage? :

```swift
func recognizeLanguage(_ text: String) -> NLLanguage? {
    let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    recognizer.processString(text)
    return recognizer.dominantLanguage
}
```

Examinons les différentes sorties : 

```swift
print(recognizeLanguage(textFR)!.rawValue)
print(recognizeLanguage(textEN)!.rawValue)
print(recognizeLanguage(textRU)!.rawValue)
print(recognizeLanguage(textJP)!.rawValue)
print(recognizeLanguage(textGR)!.rawValue)
print(recognizeLanguage(textCN)!.rawValue)
print(recognizeLanguage(textHE)!.rawValue)
```

Renvoie : 
```swift
fr
en
ru
ja
el
zh-Hant
he
```

Pour plus de clarté, nous allons créer une extension à **NLLanguage** [^6] et faire un switch sur les langues supportées :

```swift
extension NLLanguage {
    var languageStr: String {
        switch self {
            case .french:
                return "French"
            case .english:
                return "English"
            case .russian:
                return "Russian"
            case .japanese:
                return "Japanese"
            case .greek:
                return "Greek"
            case .traditionalChinese:
                return "Traditionnal Chinese"
            case .hebrew:
                return "Hebrew"
            default:
                return "Unknown"
        }
    }
}
```

Et examinons les sorties :

```swift
print(recognizeLanguage(textFR)!.languageStr)
print(recognizeLanguage(textEN)!.languageStr)
print(recognizeLanguage(textRU)!.languageStr)
print(recognizeLanguage(textJP)!.languageStr)
print(recognizeLanguage(textGR)!.languageStr)
print(recognizeLanguage(textCN)!.languageStr)
print(recognizeLanguage(textHB)!.languageStr)
```

Sorties :

```swift
French
English
Russian
Japanese
Greek
Traditionnal Chinese
Hebrew
```

Dans cette première partie, nous pouvons donc extraire la langue dominante d’un texte, voici le code complet en résumé : 

```swift
import Foundation
import NaturalLanguage

extension NLLanguage {
    var languageStr: String {
        switch self {
            case .french:
                return "French"
            case .english:
                return "English"
            case .russian:
                return "Russian"
            case .japanese:
                return "Japanese"
            case .greek:
                return "Greek"
            case .traditionalChinese:
                return "Traditionnal Chinese"
            case .hebrew:
                return "Hebrew"
            default:
                return "Unknown"
        }
    }
}

let textFR = "Il était une fois,  dans un royaume fort fort lointain"
let textEN = "Once upon a time, in a kingdom, far far away"
let textRU = "Давным-давно, в далёком-далеком королевстве"
let textJP = "むかしむかし、遠く離れた王国で"
let textGR = "Μια φορά κι έναν καιρό, σε ένα βασίλειο μακρινό"
let textCN = "很久很久以前，在一個遙遠的王國里"
let textHB = "פעם, בממלכה רחוקה רחוקה"

func recognizeLanguage(_ text: String) -> NLLanguage? {
    let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    recognizer.processString(text)
    return recognizer.dominantLanguage
}

print(recognizeLanguage(textFR)!.languageStr)
print(recognizeLanguage(textEN)!.languageStr)
print(recognizeLanguage(textRU)!.languageStr)
print(recognizeLanguage(textJP)!.languageStr)
print(recognizeLanguage(textGR)!.languageStr)
print(recognizeLanguage(textCN)!.languageStr)
print(recognizeLanguage(textHB)!.languageStr)
```

Dans le code précédent je force le unwrap car je sais exactement qu’il n’y aura pas de Nil généré ce qui serait le cas si la langue n’était pas reconnu.

Imaginons un texte en Volapük [https://fr.wikipedia.org/wiki/Volap%C3%BCk](https://fr.wikipedia.org/wiki/Volap%C3%BCk)

```swift
let textVolapuk = "O fat obas, kel binol in süls, paisaludomöz nem ola"
if let language = recognizeLanguage(textVolapuk) {
    print(language.rawValue)
} else {
    print("Unknown language")
}
```

Alors il faut faire un « safe unwrap »

## 2 Obtenir les langues possibles

Ici nous allons examiner les probabilités de correspondance des langues, grâce à la méthode **languageHypotheses** [^7]

```swift
@nonobjc
func languageHypotheses(withMaximum maxHypotheses: Int) -> [NLLanguage : Double]
```

Je vais donc créer une fonction permettant de retourner la langue possible et sa probabilité sous forme d’un dictionnaire :

```swift
func recognizeAndHypotheses(_ text: String) -> [NLLanguage: Double] {
    let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    recognizer.processString(text)
    return recognizer.languageHypotheses(withMaximum: 2)
}
```

Et en testant avec la phrase en Chinois Traditionnel :

```swift
recognizeAndHypotheses(textCN).forEach { language, prob in
    print(language.languageStr, prob)
}
```

On Obtient : 

```swift
Japanese 0.003347375662997365
Traditionnal Chinese 0.9966476559638977
```

Mais ça reste peut lisible, la fonction renvoie un dictionnaire de type :

```swift
[NSLanguage: Double]
```

je vais donc créer une extension à la classe double pour afficher les probabilités sous forme de pourcentage à 3 décimales :

L’extension est très simple : 

```swift
extension Double {
    var toPercent: String? {
        let formatter: NumberFormatter = NumberFormatter()
        formatter.maximumFractionDigits = 3
        formatter.numberStyle = .percent
        return formatter.string(from: self as NSNumber)
    }
}
```

Injectons celle ci : 

```swift
recognizeAndHypotheses(textCN).forEach { language, prob in
    print(language.languageStr, prob.toPercent!)
}
```

Et la sortie est maintenant bien plus lisible : 

```swift
Japanese 0,335 %
Traditionnal Chinese 99,665 %
```

## 3  Contraindre la recherche des langues

Bien que ce soit optionnel il est possible de fournir au  **recognizer**   des informations qui permettent d’identifier la langue, si on a déjà des indices.
Par exemple si on sait que la langue fera partie d’un certain groupe on peut ajouter une contrainte sur le choix des langues. 

Plus spécifiquement on peut donner deux types de **contraintes** :
- Une liste de langues qui contraignent la reconnaissance
- Une liste de probabilité pour certaines des langues

Imaginons une phrase en espagnol tirée du célèbre Don Quichotte :

```swift
let donQuixote: String = "Del buen suceso que el valeroso don Quijote tuvo en la espantable y jamás imaginada aventura de los molinos de viento, con otros sucesos dignos de felice recordación"
```

Et créons une méthode avec des contraintes :

```swift
func recognizeAndConstraint(_ text: String) -> [NLLanguage: Double] {
    let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    recognizer.languageConstraints = [.french, .portuguese, .spanish]
    recognizer.languageHints = [
        .french: 0.2,
        .portuguese: 0.2,
        .spanish: 0.6
    ]
    recognizer.processString(text)
    
    return recognizer.languageHypotheses(withMaximum: 3)
}
```

La sortie est de fait : 
```swift
recognizeAndConstraint(donQuixote).forEach { language, prob in
    print(language.languageStr, prob.toPercent!)
}
```

```swift
French 0 %
Portuguese 0 %
Spanish 100 %
```

En modifiant un peu les contraintes on influe sur les résultats, par exemple :

```swift
    recognizer.languageConstraints = [.portuguese, .spanish]
    recognizer.languageHints = [
        .portuguese: 0.99,
        .spanish: 0.01
    ]
```

Donnera : 

```swift
Portuguese 0,006 %
Spanish 99,994 %
```

## 4 Exemple d’application

Dans cet exemple je vais utiliser la [version 15 de Xcode](https://developer.apple.com/xcode/) actuellement en beta 6 (10 aout 2023).

L’objectif de cet exemple est d’évaluer au fur et à mesure de la saisie la langue utilisée par l’utilisateur.

Pour commencer nous allons créer deux extensions les mêmes que celles mentionnées au dessus :

```swift
extension Double {
    var toPercent: String? {
        let formatter = NumberFormatter()
        formatter.maximumFractionDigits = 3
        formatter.numberStyle = .percent
        return formatter.string(from: self as NSNumber)
    }
}
```

```swift
extension NLLanguage {
    var flag: String {
        switch self {
            case .french:
                return "🇫🇷"
            case .english:
                return "🇬🇧"
            case .spanish:
                return "🇪🇸"
            case .german:
                return "🇩🇪"
            default:
                return "🏴‍☠️"
        }
    }
    
    var languageString: String {
        switch self {
            case .french:
                return "French"
            case .english:
                return "English"
            case .spanish:
                return "Spanish"
            case .german:
                return "German"
            default:
                return "Unknown"
        }
    }
}
```

Ensuite nous allons créer une structure qui permettra de stocker les hypothèses :

```swift
struct Hypotheses: Hashable {
    let language: NLLanguage
    let prob: Double
    
    var languageStr: String {
        return language.languageString
    }
    
    var flag: String {
        return language.flag
    }
}
```

Maintenant passons  à la mise en place la partie reconnaissance, pour ce faire je vais créer une classe et utiliser le framework Observation pour générer la « source of truth » , tout d’abord il faut penser à importer le framework [**Observation**](https://developer.apple.com/documentation/observation) [^8]

Nous créons donc une class **Recognizer** annotée avec la macro **Observable**, qui contient deux propriétés observables :

```swift
@Observable
final class LangRecognizer {
    var text: String = ""
    var hypotheses: [Hypotheses] = [Hypotheses]()
    
}
```

Maintenant nous ajoutons la méthode permettant la reconnaissance de la langue :

```swift
    func recognize() {
        recognizer.processString(text)
        let hypothesesDictionnary = recognizer.languageHypotheses(withMaximum: 3)
        
        hypotheses = hypothesesDictionnary.map { lang, prob in
            return Hypotheses(language: lang, prob: prob)
        }
    }
```

Pour l’interface utilisateur, quelque chose de simple un [texteditor](https://developer.apple.com/documentation/swiftui/texteditor) et un composant [list](https://developer.apple.com/documentation/swiftui/list) qui affichera la liste des hypothèses :

```swift
struct ContentView: View {
    
    @State private var langRecognizer = LangRecognizer()
    
    var body: some View {
        VStack {
            TextEditor(text: $langRecognizer.text)
                .overlay {
                    RoundedRectangle(cornerRadius: 12)
                        .stroke(lineWidth: 2)
                    
                }
            List {
                ForEach(1..<6, id: \.self) { i in
                    Text("\(i)")
                }
            }
            .listStyle(.plain)
        }
        .padding()
    }
}
```

Il suffit maintenant d’ _observer_ les changements du composant [texteditor](https://developer.apple.com/documentation/swiftui/texteditor) :
```swift
TextEditor(text: $langRecognizer.text)
    .overlay {
        RoundedRectangle(cornerRadius: 12)
            .stroke(lineWidth: 2)
        
    }
    .onChange(of: langRecognizer.text) { _, _ in
        langRecognizer.recognize()
    }
```

Puis d’afficher les hypothèses dans le composant [list](https://developer.apple.com/documentation/swiftui/list): 

```swift
List {
    ForEach(langRecognizer.hypotheses, id: \.self) { hypothese in
        HStack {
            Text(hypothese.flag)
            Text(hypothese.languageStr)
            Spacer()
            Text(hypothese.prob.toPercent!)
        }
    }
}
```

On peut aussi utiliser une variable calculée pour afficher les hypothèses dans l’ordre des probabilités :

```swift
var sortedHypothese: [Hypotheses] {
    return langRecognizer.hypotheses.sorted { $0.prob > $1.prob }
}
```

Et l’injecter dans la liste, ce qui donne le code complet suivant : 

```swift
//
//  ContentView.swift
//  LanguageDetectioniOS17
//
//  Created by David Tavan on 02/08/2023.
//

import SwiftUI
import NaturalLanguage

extension Double {
    var toPercent: String? {
        let formatter = NumberFormatter()
        formatter.maximumFractionDigits = 3
        formatter.numberStyle = .percent
        return formatter.string(from: self as NSNumber)
    }
}

extension NLLanguage {
    var flag: String {
        switch self {
            case .french:
                return "🇫🇷"
            case .english:
                return "🇬🇧"
            case .spanish:
                return "🇪🇸"
            case .german:
                return "🇩🇪"
            default:
                return "🏴‍☠️"
        }
    }
    
    var languageString: String {
        switch self {
            case .french:
                return "French"
            case .english:
                return "English"
            case .spanish:
                return "Spanish"
            case .german:
                return "German"
            default:
                return "Unknown"
        }
    }
}

struct Hypotheses: Hashable {
    let language: NLLanguage
    let prob: Double
    
    var languageStr: String {
        return language.languageString
    }
    
    var flag: String {
        return language.flag
    }
}

@Observable
final class LangRecognizer {
    var text: String = ""
    var hypotheses: [Hypotheses] = [Hypotheses]()
    
    private let recognizer: NLLanguageRecognizer = NLLanguageRecognizer()
    
    func recognize() {
        recognizer.processString(text)
        let hypothesesDictionnary = recognizer.languageHypotheses(withMaximum: 3)
        
        hypotheses = hypothesesDictionnary.map { lang, prob in
            return Hypotheses(language: lang, prob: prob)
        }
    }
}

struct ContentView: View {
    
    @State private var langRecognizer = LangRecognizer()
    
    var sortedHypothese: [Hypotheses] {
        return langRecognizer.hypotheses.sorted { $0.prob > $1.prob }
    }
    
    var body: some View {
        VStack {
            TextEditor(text: $langRecognizer.text)
                .overlay {
                    RoundedRectangle(cornerRadius: 12)
                        .stroke(lineWidth: 2)
                    
                }
                .onChange(of: langRecognizer.text) { _, _ in
                    langRecognizer.recognize()
                }
            List {
                ForEach(sortedHypothese, id: \.self) { hypothese in
                    HStack {
                        Text(hypothese.flag)
                        Text(hypothese.languageStr)
                        Spacer()
                        Text(hypothese.prob.toPercent!)
                    }
                }
            }
            .listStyle(.plain)
        }
        .padding()
    }
}

#Preview {
    ContentView()
}

```






[^1]:	[https://developer.apple.com/documentation/naturallanguage/identifying\_the\_language\_in\_text](https://developer.apple.com/documentation/naturallanguage/identifying_the_language_in_text "Identifying the language in text")

[^2]:	[https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer](https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer "NLLanguageRecognizer")

[^3]:	[https://developer.apple.com/documentation/naturallanguage/nllanguage](https://developer.apple.com/documentation/naturallanguage/nllanguage "NLLanguage")

[^4]:	[https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer](https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer "NLLanguageRecognizer")

[^5]:	[https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer/2976562-processstring](https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer/2976562-processstring "ProcessString")

[^6]:	[https://developer.apple.com/documentation/naturallanguage/nllanguage](https://developer.apple.com/documentation/naturallanguage/nllanguage "NLLanguage")

[^7]:	[https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer/2976561-languagehypotheses](https://developer.apple.com/documentation/naturallanguage/nllanguagerecognizer/2976561-languagehypotheses)

[^8]:	[https://developer.apple.com/documentation/observation](https://developer.apple.com/documentation/observation)