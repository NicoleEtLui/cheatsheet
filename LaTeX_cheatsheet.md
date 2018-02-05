### LaTeX

#### Structures
##### page de garde

* le titre du document `\title{votre titre}` 
* l'auteur `\author{les noms des auteurs}` 
* la date `\date{la date que vous souhaitez}`

__Ces trois �l�ments sont introduits avant la commande `\begin{document}`, et une quatri�me commande `\maketitle` se place juste apr�s `\begin{document}` afin de faire comprendre � LaTeX que vous souhaitez composer une page de garde avec les trois �l�ments cit�s plus haut.__

##### marges 
* `usepackage{layout}` r�cup�ration du package layout}
* `\layout` instanciation d'un gabarit visible apr�s compilation
* `usepackage{geometry}` permet de changer la taille des marges. 
ex : `\usepackage[top=2cm, bottom=2cm, left=2cm, right=2cm]{geometry}`

##### interlignage
* package `setspace`
* mot cl� `onehalfspace`, `doublehalfspace`, pour limiter cela � une partie de 
la composition : `\begin{onehalfspace}...\end{onehalfspace}`
* interlignage personnalis� ???

##### listes 
* "ul"
```
\begin{itemize}
\item item1
\item item2
\item[@] item3 %utilise un @ comme puce
\item[0] item4 %utilise un 0 comme puce 
...
\end{itemize}
```

* "ol"
```
\begin{enumerate}
\item item1
\item item2
\item[@] item3 %utilise un @ comme puce
\item[0] item4 %utilise un 0 comme puce 
...
\end{enumerate}
```
* "dl"
```
\begin{description}
\item[Un canard :] bestiole qui fait coin.
\item[Un poulpe :] bestiole qui fait bloub.
\item[Un ornithorynque :] bestiole qui fait rire.
\item[Un ours :] bestiole qui fait mal.
\end{description}
```

##### en-t�te et pied de page 
* le style plain : il permet d'ins�rer le num�ro de page au milieu du pied de 
page ;
* le style headings : il permet d'ins�rer le nom du chapitre et le num�ro de 
page en en-t�te. Le pied de page est vide ;
* le style empty : l'en-t�te et le pied de page sont vides.

* `\pagestyle{nom du style}`

##### en vrac
* `\apendix` transforme chaitre en annexe ( d'autres modifications de 
numerotation existent, voir `\frontmatter`, `\mainmatter`, `\backmatter` )

* `\chapter` ne fonctionne que dans les rapports et les livres

* `*` en fin de mot permet de cr�er des parties, paragraphes, sections, ... sans
num�ro ex: `\chapter*{nom du chapitre}`

* `\documentclass[option1, option2, option3]{type}` permet de g�rer des choses tels que format du papier, taille de police, ...

![documentclass](./IMG/documentclass.PNG)
![documentclass](./IMG/documentclass_options.PNG)