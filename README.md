# sisob
Image Sonification by Convolution with White Noise · Sonificació d’imatges per convolució amb soroll blanc

Català -- Scroll down for English

SICSOB – Sonificació d’imatges per convolució amb soroll blanc

Resum

SICSOB (acrònim de «Sonificació d’imatges per convolució amb soroll blanc») és un petit programa de recerca escrit en Max/MSP.  L’objectiu és convertir imatges en sons llegint-les de manera seqüencial d’esquerra a dreta, com si fossin partitures: les freqüències greus s’assignen a la part inferior de la imatge i les agudes a la part superior.  Cada columna de la imatge es considera un espectre instantani recuperable mitjançant eines com la transformada ràpida de Fourier (FFT).

Aquest enfocament va néixer per analitzar espectrogrames de la missió Dynamics Explorer 1 (DE‑1) Plasma Wave Investigation, que va llançar dos satèl·lits el 3 d’agost de 1981 per estudiar com es relacionen la magnetosfera, la ionosfera i l’atmosfera ￼.  L’instrument de ones de plasma va enregistrar fenòmens com la radiació kilohertz auroral, el xiulet auroral i altres emissions electromagnètiques ￼.  L’arxiu de la Universitat d’Iowa ofereix espectrogrames en format GIF, on cada fitxer codifica la data inicial i l’ample de banda ￼.

Funcionament
	1.	Lectura de la imatge: El patch escaneja l’arxiu columna a columna.  L’eix horitzontal correspon al temps i l’eix vertical a la freqüència.
	2.	Conversió a filtre: Cada columna es interpreta com un filtre de 512 bandes i es desa en un vector de 1×512 mostres.
	3.	Convolució amb soroll: El filtre s’aplica a la transformada de soroll blanc multiplicant les parts real i imaginària per cada valor del vector.  Aquest procés es realitza amb una FFT de 4096 mostres; d’aquesta manera s’obtenen espectres comprimits i es facilita la identificació de les característiques sonores.  El subpatcher pfft~ filtrofftm512.maxpat 4096 2 és el responsable del càlcul.
	4.	Synthesi sonora: Cada columna filtrada es reconstrueix al domini temporal i es concatena amb les restants per formar una seqüència contínua.  Com que només es retenen les freqüències presents al filtre, aquest tipus de processament s’anomena sovint fftfilter.

Requisits
	•	Max 7 o posterior. Es pot descarregar una versió de prova des de cycling74.com.
	•	Equip amb sortida d’àudio. El processament pot ser exigent amb imatges d’alta resolució.
	•	Opcional: espectrogrames de la missió DE‑1.  L’arxiu de la Universitat d’Iowa permet descarregar fitxers en format GIF (YYDDDHHMM_s/cID_bandwidth.gif) on YYDDDHHMM és la data i l’hora, s/cID el satèl·lit i bandwidth l’ample de banda ￼.

Ús
	1.	Obriu el patch principal (SICSOB.maxpat) a Max/MSP.
	2.	Seleccioneu una imatge:
	•	Utilitzeu l’objecte umenu de la secció Anàlisi per escollir un fitxer d’un directori predefinit, o envieu el missatge importmovie, bang per carregar qualsevol arxiu del disc.
	3.	Ajusteu el llindar d’energia: La caixa numerada <r fint> permet fixar el percentatge de píxels amb més energia que seran considerats.  Els píxels més foscos s’eliminen per reduir el soroll.
	4.	Definiu la submatriu: Les caixes <r offx>, <r offy>, <r dimx> i <r dimy> determinen l’ofset i la mida (en columnes i files) de la regió de la imatge que es sonificarà.  Això permet eliminar elements com regles o etiquetes que sovint ocupen els marges.
	5.	Inicieu la sonificació: Pitgeu el botó d’inici perquè el patch llegeixi cada columna, la filtri amb soroll blanc i reprodueixi el resultat.

Estructura del patcher

Anàlisi
	•	Selecció de fitxers: L’objecte umenu mostra els arxius disponibles en un directori conegut.  També es pot utilitzar importmovie per carregar qualsevol imatge del disc.
	•	Llindar d’energia: <r fint> selecciona els píxels més energètics i elimina el soroll de fons.
	•	Submatriu: Els paràmetres offx, offy, dimx, dimy defineixen la regió d’interès dins de la imatge.
	•	Conversió a grisos: Un cop triada la regió, els valors RGB es converteixen a grisos (lluminositat), es filtren les parts menys rellevants i es normalitzen.  El resultat es desa a jit.matrix n1.

Adequació
	•	Extracció de la columna: S’extreu una columna sencera de jit.matrix n1 i s’orienta horitzontalment.  Aquesta columna s’emmagatzema a jit.buffer~ tdye i s’utilitza per filtrar el soroll blanc.
	•	Paràmetres de filtre:
	•	<r pivec>: defineix el punt de la columna a partir del qual s’inicia la sonificació, ajustant l’altura de l’espectre.
	•	<r dimvec>: controla la llargada de la columna sonificada.  Un valor de 512 utilitza tota la columna; valors menors actuen com a filtre passa-alts retallant les parts baixes de l’espectre.

Lectura
	•	Modes de lectura:
	•	<r totlect>: activa la lectura seqüencial completa de la regió seleccionada.
	•	<r parclect>: permet llegir només una part de la imatge.
	•	<r nolect>: atura la lectura seqüencial.
	•	Configuració temporal:
	•	<r desd> i <r finsa> defineixen l’inici i el final de la zona llegida en mode parcial.
	•	<r durant> especifica el temps d’execució de la lectura parcial.
	•	<r durtotal> fixa la durada de la lectura completa de la imatge.
	•	<s postemp>: en mode nolect, permet seleccionar una columna aïllada per escoltar la seva sonificació.

Auxiliars
	•	Navegador web (p web): un missatge com url https://space.physics.uiowa.edu/plasma-wave/de/archive/wideband/ obre una finestra amb l’arxiu de dades de la missió DE‑1.
	•	Representacions (p representacions): mostra la imatge i la regió que s’està sonificant.  És útil disposar d’un segon monitor; un control lliscant indica la columna activa i, en mode nolect, permet triar-la manualment.

Generació de so
	•	Sonificació: El patcher pfft~ filtrofftm512.maxpat 4096 2 realitza la multiplicació espectral entre el soroll blanc i la columna filtrada, i l’objecte info~ tdye proporciona informació sobre la memòria intermèdia.
	•	Control de volum: Un control lliscant ajusta el nivell de sortida d’àudio.

Treballant amb els espectrogrames de DE‑1

Els valors per defecte del patch estan pensats per a les imatges del projecte Dynamics Explorer 1 Plasma Wave Investigation.  Aquesta missió va proporcionar un gran nombre d’espectrogrames de potència versus temps i freqüència.  Els fitxers en format GIF es poden consultar al node de la Universitat d’Iowa: en seleccionar la nau, es mostra un directori amb fitxers YYDDDHHMM_s/cID_bandwidth.gif ￼.  Per obtenir les dades originals, cal contactar amb l’arxiu, ja que els conjunts de dades són voluminosos ￼.

Llicència i crèdits

SICSOB es distribueix amb finalitats educatives i de recerca.  Utilitza Max/MSP per al processament d’imatges i so.  Els espectrogrames de la missió DE‑1 són propietat de la Universitat d’Iowa i de la Universitat de Stanford, i són accessibles a través de l’Space Physics Data Center ￼ ￼.

__________________________________________________________________________________________
English -- Català més amunt

SICSOB – Image Sonification by Convolution with White Noise

Overview

SICSOB (Catalan acronym of “Sonification of Images by Convolution with White Noise”) is a small research‑oriented program written in Max/MSP.  It reads an image sequentially from left to right, treating each column as a musical score: low frequencies are mapped to the bottom of the image and high frequencies to the top.  Each column is considered an instantaneous spectrum that can be recovered using tools like the Fast Fourier Transform (FFT).

The approach was originally developed to explore frequency–time spectrograms from the Dynamics Explorer 1 (DE‑1) Plasma Wave Investigation.  The DE‑1 mission launched two satellites on 3 August 1981 to investigate magnetosphere–ionosphere–atmosphere coupling ￼.  Its plasma‑wave instrument recorded phenomena such as auroral kilometric radiation, auroral hiss and other emissions ￼.  The University of Iowa archives these recordings as low‑resolution GIF spectrograms; each filename encodes the start time, spacecraft ID and bandwidth ￼.

How It Works
	1.	Image scanning: The patch scans the image column by column.  The horizontal axis represents time, and the vertical axis represents frequency.
	2.	Filter generation: Each column of pixels is interpreted as a 512‑band filter and stored as a 1×512 vector.
	3.	Noise convolution: The filter is applied to the FFT of a white‑noise signal by multiplying the real and imaginary parts of the noise spectrum by each element of the vector.  The process uses a 4096‑sample FFT, producing compressed spectra that emphasise features in the image.  The sub‑patcher pfft~ filtrofftm512.maxpat 4096 2 performs this calculation.
	4.	Sound synthesis: The filtered column is converted back to the time domain and concatenated with the others to form a continuous sound stream.  Since only the frequencies present in the filter are retained, this process is often called an FFT filter.

Requirements
	•	Max 7 or later. The program can run on demo versions available from cycling74.com.
	•	Computer with audio output. High‑resolution images can be CPU‑intensive.
	•	Optional: DE‑1 spectrograms. You can download GIF spectrograms (YYDDDHHMM_s/cID_bandwidth.gif) from the University of Iowa’s archive ￼.

Usage
	1.	Open the patch: Start Max/MSP and open SICSOB.maxpat.
	2.	Load an image:
	•	Use the umenu object in the Analysis section to select a file from the predefined directory, or
	•	Send the message importmovie, bang to load any image from your disk.
	3.	Set the energy threshold: The number box <r fint> lets you choose the fraction of the highest‑energy pixels to keep; pixels below this threshold are discarded to reduce noise.
	4.	Define the region of interest: Four number boxes — <r offx>, <r offy>, <r dimx> and <r dimy> — specify the offset and size (in columns and rows) of the region of the image to sonify.  Cropping eliminates non‑sonifiable elements such as rulers or legends.
	5.	Start sonification: Press the start button.  The patch scans each column in the selected region, filters it with white noise and plays the result.

Patcher Structure

Analysis
	•	File selection: The umenu object lists available image files.  You can also use importmovie to load arbitrary images.
	•	Energy threshold: <r fint> selects the most energetic pixels and suppresses background noise.
	•	Region of interest: The parameters offx, offy, dimx, dimy define the rectangle to be processed.
	•	Grayscale conversion: Once the region is chosen, RGB values are converted to grayscale (luminosity).  Low‑intensity pixels are filtered out, and the resulting matrix is normalised and stored in jit.matrix n1.

Adaptation
	•	Column extraction: A full column of jit.matrix n1 is extracted and reoriented horizontally.  This 512‑sample vector is stored in jit.buffer~ tdye and used to filter the noise spectrum.
	•	Filter parameters:
	•	<r pivec> sets the starting index of the column for sonification; adjusting this parameter shifts the spectral content.
	•	<r dimvec> determines how many samples of the column are used.  A value of 512 uses the entire column; smaller values act as a high‑pass filter by cutting off lower frequencies.

Reading
	•	Scanning modes:
	•	<r totlect> enables full sequential reading of the selected region.
	•	<r parclect> activates partial reading of the image.
	•	<r nolect> stops the sequential scan.
	•	Temporal controls:
	•	<r desd> and <r finsa> set the start and end columns when partial reading is selected.
	•	<r durant> specifies the duration (in seconds) for the partial scan.
	•	<r durtotal> sets the duration for a full scan of the image.
	•	<s postemp> allows you to select a single column for isolated playback when sequential reading is stopped (nolect mode).

Auxiliary
	•	Web access (p web): Sending a message like url https://space.physics.uiowa.edu/plasma-wave/de/archive/wideband/ opens a browser window with the DE‑1 data archive.
	•	Representations (p representations): Displays the image and the currently sonified region; useful on a second monitor.  A slider shows which column is being read and lets you select a column manually in nolect mode.

Sound Generation
	•	Sonification: The patcher pfft~ filtrofftm512.maxpat 4096 2 multiplies the noise spectrum by the filter and reconstructs the audio.  The object info~ tdye provides buffer information.
	•	Volume control: A slider adjusts the audio output level.

Working with DE‑1 Spectrograms

SICSOB’s default settings are tailored for spectrograms from the Dynamics Explorer 1 Plasma Wave Investigation.  These images display plasma‑wave power versus time and frequency.  The University of Iowa’s archive presents directories of GIF files; each filename encodes the start time and bandwidth ￼.  Original data sets are large and can be requested by contacting the archive custodians ￼.

License & Credits

SICSOB is provided for educational and research purposes.  It relies on Max/MSP for image processing and sound synthesis.  Spectrogram data from the DE‑1 Plasma Wave Investigation were collected by the University of Iowa and Stanford University and are distributed through the University of Iowa’s Space Physics Data Center ￼ ￼.



