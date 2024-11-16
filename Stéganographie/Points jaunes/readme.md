J’ai tout d’abord ouvert l’image du challenge dans GIMP, et j’ai choisi de n’afficher que le channel bleu, j’ai alors remarqué un rectangle contenant des points disposés comme sur un quadrillage en haut à droite.

Afin de décoder ces points, j’ai utilisé une interface graphique que j’ai trouvé sur le site du langage TCL : Docucolor Decoder

J’ai commencé par installer TCL et TK :
```
apt install tcl tk
```
Puis j’ai copié le code de Docucolor Decoder dans un fichier docucolor.tcl
Je copie le code ici dans l’hypothèse il ne serait plus disponible sur le site original à l’avenir.
```
bind all <Escape> {exit}
 
option add *Button.relief flat
option add *Button.foreground white
option add *Button.background blue
option add *Button.width 14
option add *Label.foreground yellow
option add *Label.background darkblue
option add *Label.width 104
 
proc About {} {
set w .about
catch {destroy $w}
toplevel $w
.about configure -bg black
wm title $w "About Docucolor Decoder"
set txt "Docucolor Decoder - (v0.1 - Dec 2017) - Gerard Sookahet\n
Docucolor Decoder can decode small yellow tracking dots pattern inserted automatically
to identify the color laser printer and the date when the document was produced."
message $w.msg -justify left -aspect 250 -relief flat -bg black -fg lightblue -text $txt
button $w.bquit -text " OK " -command {destroy .about}
pack $w.msg $w.bquit
}
 
proc CreateDot {x y tag} {
.f1.c create oval [list $x $y [expr {$x+20}] [expr {$y+20}]] -tag $tag -fill darkblue
.f1.c bind $tag <1> "ChangeDotColor $tag"
}
 
proc ChangeDotColor {tag} {
set color [.f1.c itemcget $tag -fill]
if {$color eq "darkblue"} then {
.f1.c itemconfigure $tag -fill yellow
} elseif {$color eq "yellow"} then {
.f1.c itemconfigure $tag -fill darkblue
}
Decode
}
 
proc Reset {col row} {
set sep :
foreach i $col {
 foreach j $row {
        .f1.c itemconfigure $i$sep$j -fill darkblue
 }
}
foreach j [lrange $row 1 end] {.f1.c itemconfigure 9$sep$j -fill yellow}
foreach j [lrange $row 1 2]   {
 .f1.c itemconfigure 4$sep$j -fill midnightblue
 .f1.c itemconfigure 5$sep$j -fill midnightblue
}
foreach j [lrange $row 1 3] {.f1.c itemconfigure 6$sep$j -fill midnightblue}
foreach j $row {
 .f1.c itemconfigure  2$sep$j -fill midnightblue
 .f1.c itemconfigure  3$sep$j -fill midnightblue
 .f1.c itemconfigure  8$sep$j -fill midnightblue
}
.f1.c itemconfigure 1:64 -fill midnightblue
 
set ::code ""
}
 
 
proc GetCol {col row} {
set l {}
foreach i $col {
 set d 0
 foreach j $row {
   set tag [join [list $i $j] :]
   if {[.f1.c itemcget $tag -fill] eq "yellow"} then {incr d $j}
 }
 lappend l $d
}
return $l
}
 
proc CheckParity {col row} {
set lrow {}
set lcol {}
 
foreach i $col {
 set d 0
 foreach j $row {
        set tag [join [list $i $j] :]
        if {[.f1.c itemcget $tag -fill] eq "yellow"} then {incr d}
 }
lappend lcol [expr {$i*(($d % 2) ^ 1)}]
}
 
foreach j $row {
 set d 0
 foreach i $col {
        set tag [join [list $i $j] :]
        if {[.f1.c itemcget $tag -fill] eq "yellow"} then {incr d}
 }
 lappend lrow [expr {$j*(($d % 2) ^ 1)}]
}
foreach k {2 3 8 0} {
 set lcol [lsearch -inline -all -not -exact $lcol $k]
}
set lrow [lsearch -inline -all -not -exact $lrow 0]
 
return [concat ROW $lrow COL $lcol]
}
 
proc Decode {} {
 
set col [list 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14]
set row [list 0 64 32 16 8 4 2 1]
 
set lrow    [lrange $row 1 end]
 
set serial [GetCol [lreverse [lrange $col 10 end]] $lrow]
set ymd    [GetCol [lreverse [lrange $col 5 7]] $lrow]
set hm     [GetCol [list 4 1] $lrow]
 
set year  [lindex $ymd 0]
set month [lindex $ymd 1]
set day   [lindex $ymd 2]
 
set hour [lindex $hm 0]
set min  [lindex $hm 1]
 
set serial [join $serial ""]
if {$year < 70 || $year > 99} then {incr year 2000} else {incr year 1900}
set date [join [concat $year [expr {$month < 13 ? $month : "MM"}] $day] "-"]
set time [join [concat [expr {$hour < 25 ? $hour : "hh"}] [expr {$min < 61 ? $min : "mm"}]] ":"]
 
set pc [CheckParity $col $row]
if {$pc eq "ROW COL"} {set pc "OK"}
 
set ::code "Date: $date at $time -- Printer Serial Number: $serial  -- Parity Check: $pc"
}
 
. configure -bg black
wm title . "Docucolor Decoder"
 
set f1 [frame .f1 -relief flat -bg black]
set f3 [frame .f3 -relief flat -bg black]
set f4 [frame .f4 -relief flat -bg black]
pack $f1 $f3 $f4 -pady 2
 
pack [canvas .f1.c -bg black -width 630 -height 390]
 
set col [list 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14]
set row [list 0 64 32 16 8 4 2 1]
 
set x 50
set y 30
.f1.c create text $x $y -text parity -angle 90 -fill white
foreach {s c} {minute white unused grey unused grey hour white day white month white year white unused grey} {
.f1.c create text [incr x 40]  $y -text $s -angle 90 -fill $c
}
.f1.c create text 530 $y -text serial -fill white
.f1.c create line 445 [expr {$y+10}] 620 [expr {$y+10}] -fill blue
 
set x 50
foreach i $col {
.f1.c create text $x 70 -text $i -fill white
incr x 40
}
set y 90
foreach j [concat parity [lrange $row 1 end]] {
.f1.c create text 20 $y -text $j -fill white
incr y 40
}
 
set x 40
set sep :
foreach i $col {
set y 80
foreach j $row {
   CreateDot $x $y $i$sep$j
   incr y 40
}
incr x 40
}
 
label $f3.l -textvariable code
pack $f3.l -pady 4
 
button $f4.b1 -text Reset -command {Reset $::col $::row}
button $f4.b2 -text About -command {About}
button $f4.b3 -text Exit  -command {exit}
pack {*}[winfo children $f4] -side left -padx 2 -pady 2
 
Reset $col $row
```

J’ai alors lancé le programme avec la commande suivante :
```
wish docucolor.tcl
```
Pour finir j’ai entré dans l’interface graphique les points que j’avais découvert dans l’image et je n’avais alors plus qu’à recopier les différentes valeurs pour les mettre au format demandé pour le flag.
