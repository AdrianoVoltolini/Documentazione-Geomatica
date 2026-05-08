PPK partendo da raw2go
=======================

Step 1: recupero dei file e programmi necessari
-----------------------------------------------

1. Recuperare file .ubx creato da raw2go dal tablet. Lo si può trovare in “memoria interna/Download/(data in cui si è fatta la geolocalizzazione)”.
   
   File di prova di 1 ora con ricevitore GNSS posizionato a Sant'Eufemia a Maiella: :download:`seufemia1.ubx <assets/ppk/seufemia1.ubx>`

2. Scaricare i dati RINEX da https://gnssnet.regione.abruzzo.it/:
 
   * Premere su 'Download Rinex', selezionare la stazione più vicina alla posizione di rilevamento del ricevitore GNSS, e premere 'Dati Rinex'.
  
   .. image:: assets/ppk/images/abruzzo_elenco.png

   * Accedere con le proprie credenziali o registrarsi se non si ha un account.
   * Nella finestra della stazione permanente, selezionare il giorno e orario in cui è stato fatto il rilevamento con il ricevitore GNSS, e fissare l'intervallo di campionamento dei dati raccolti a 5 secondi. Premere [CreaRinex].

   .. image:: assets/ppk/images/abruzzo_stazione.png

   * Revisionare gli input e premere [Conferma]. Comincerà l'elaborazione dei file.
   * Una volta finita l'elaborazione, scaricare il file con formato .25O. Se il browser segnala che 'il file non può essere scaricato in modo sicuro', posizionare il cursore del mouse sull'avviso, premere su [...] a destra del cestino, premere su [Mantieni] e infine su [Conserva comunque].

   .. image:: assets/ppk/images/abruzzo_download.png

   File .25O di prova: :download:`smra3340.25O <assets/ppk/smra3340.25O>`. 

3. Scaricare RTKLIB dal `sito ufficiale`_ oppure da qui: :download:`RTKLIB_EX_2.5.0.zip <assets/ppk/RTKLIB_EX_2.5.0.zip>`
   
   Manuale di RTKLIB (datato ma comunque utile): :download:`manual_demo5.pdf <assets/ppk/manual_demo5.pdf>`


Step 2: conversione da .ubx a (.obs + .nav)
-------------------------------------------

1. Estrarre la cartella compressa di RTKLIB.
2. Avviare l’applicazione rtklaunch
   
 .. image:: assets/ppk/images/rtklaunch_icon.png

 .. image:: assets/ppk/images/rtklaunch_gui.png

3. Premere sulla seconda icona (RTKCONV). Si aprirà la finestra di RTKCONV.
   
 .. image:: assets/ppk/images/rtkconv.png

4. Sotto 'RTCM, RCV RAW or RINEX OBS ?', premere [...] e selezionare il file .ubx prodotto da raw2go. Sotto 'RINEX OBS/NAV/GNAV/HNAV/QNAV/LNAV/CNAV/INAV/ and SBS' compariranno automaticamente i percorsi dei file che verranno creati in seguito.

5. Premere [# Options...]: 

   .. image:: assets/ppk/images/rtkconv_options.png

   * Selezionare i Satellite Systems di interesse. 

   * Selezionare I GNSS Signals utilizzati dal ricevitore (L1 e L2) 

   * Premere [OK]. La finestra Options si chiuderà.

6. Premere [> Convert ] nella finestra di RTKCONV. I file .obs e .nav verranno creati nei percorsi visualizzati. 


Step 3: salvataggio dei dati DOP
--------------------------------------------------------------

1. Nella finestra di RTKCONV, Premere su [@ Plot...]. Si aprirà una nuova finestra.
2. Aspettare qualche secondo affinché la finestra carichi i dati. 
3. Andare su File -> Save # of Sats/DOP...

 .. image:: assets/ppk/images/rtkplot_dop.png

4. Scegliere dove salvare il file e il nome, e premere [salva].

Step 4: PPK
-----------

1. Nella finestra di RTKCONV, premere [Process...]. Si aprirà la finestra di RTKPOST.

   .. image:: assets/ppk/images/rtkpost.png

2. Sotto “RINEX OBS: Rover ?” dovrebbe già comparire il file .obs creato alla fine dello Step 2. Nel caso si volesse elaborare un file diverso, premere [...] e selezionarlo. 

3. Sotto “RINEX OBS: Base Station”, premere [...] e cercare il file .25O scaricato nello Step 1. 

4. Sotto “RINEX NAV/CLK, SP3, BIA/BSX, FCB, IONEX, SBS/EMS, or RTCM” premere [...] e selezionare il file .nav creato alla fine dello Step 2. 

5. Premere [# Options...]:

   .. image:: assets/ppk/images/rtkpost_options.png
   
   * Premere [Load...], navigare nella cartella di RTKLIB e selezionare il file “f9p_ppk.conf”. Questo file contiene la configurazione ottimale per fare Post Processing Kinematics con il chip F9P, presente all'interno del ricevitore GNSS. 
   
   * selezionare i satelliti di interesse.
   * Premere [OK]. La finestra Options si chiuderà.

6. Nella finestra di RTKPOST, premere [> Execute]. Un file con formato .pos dovrebbe essersi creato nel percorso visualizzato in basso, assieme ad altri file.


Step 5: Conversione da .pos a .gpx
----------------------------------
1. Nella finestra di RTKPOST, Premere il pulsante [KML/GPX...]. Si aprirà la finestra KML/GPX Converter.

 .. image:: assets/ppk/images/kmlgpx_converter.png

2. Assicurarsi che l'Output Format sia GPX.

3. Premere [Convert]. Il file .gpx dovrebbe essersi creato nel percorso visualizzato in basso.


Step 6: Conclusioni
-------------------

1. Chiudere tutte le finestre di RTKLIB. Le configurazioni delle varie finestre vengono salvate in automatico. Nel caso si volesse resettarle, entrare nella cartella di RTKLIB ed eliminare i file di tipo “Impostazioni di configurazione” creati recentemente. 

 .. image:: assets/ppk/images/file_configurazione.png

2. Aprire il file .gpx in QGIS: il .gpx dovrebbe contenere 5 layers, 3 di punti e 2 di linee. Tuttavia, un layer di punti e un layer di linee sembrano vuoti. Il layer 'waypoints' contiene anche la posizione della stazione permanente utilizzata. La qualità della correzione tramite PPK è indicata per ogni singolo punto come attributo nella colonna 'fix'. I possibili valori sono, in ordine dal migliore al peggiore: fix, float, dgps, single.

 .. image:: assets/ppk/images/qgis.png

.. _sito ufficiale: https://github.com/rtklibexplorer/RTKLIB/releases
