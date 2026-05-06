Creare DSM con colmap e CloudCompare
====================================

Step 1: Preparazione della cartella progetto e dei programmi
------------------------------------------------------------

1. Creare come cartella progetto una cartella vuota e posizionare le foto stereo di interesse in una sua sottocartella.
2. Scaricare dal `sito ufficiale di colmap`_ il programma.
   
   NB: colmap necessita di una scheda video NVIDIA che supporti CUDA.
3. Scaricare dal `sito ufficiale di CloudCompare`_ il programma ed installarlo.

Step 2: Ricostruzione sparsa della nuvola di punti con colmap
-------------------------------------------------------------
1. Estrarre la cartella compressa di colmap.
2. Avviare il file batch 'COLMAP.bat'. Si aprirà una finestra del terminale e dopo qualche secondo l'interfaccia grafica di colmap (al primo avvio ci metterà un po\').
   
   .. image:: assets/dsm/images/colmap.png

3. Creare un nuovo progetto premendo su File > New project. Si aprirà una finestrella che ci chiederà un database e le foto stereo. 

   * Per il database: premere [New], navigare all'interno della cartella progetto creata nello Step 1 e creare un nuovo database.
   * Per le immagini: premere [Select] e selezionare la sottocartella contenente le foto stereo.
   * Premere [Save].
  
   .. image:: assets/dsm/images/colmap_new_project.png

4. Premere File > Save project. Salvare il file .ini all'interno della cartella progetto e ricordarsi di salvare dopo ogni prossimo passaggio di questo step.

5. Premere Processing > Feature extraction. Aumentare il numero di features da estrarre da ogni immagine aumentando il parametro sift.max_num_features. Premere [Extract].
   
   .. image:: assets/dsm/images/colmap_feature_extraction.png

6. Chiudere la finestra di Feature extraction e premere Processing > Feature matching. Aumentare il numero di accoppiamenti tra le features delle immagini aumentando il parametro max_num_matches. Premere [Run].
7. Chiudere la finestra di Feature matching e premere Reconstruction > Start Reconstruction.
   
   .. image:: assets/dsm/images/colmap_sparse_reconstruction.png

Step 3: Ricostruzione densa della nuvola di punti con colmap
------------------------------------------------------------
1. Premere Reconstruction > Dense reconstruction. Si aprirà una nuova finestra.
2. Premere [Select] e selezionare la cartella progetto.
3. Premere [Undistortion]. Si creeranno diversi nuovi file e sottocartelle all'interno della cartella progetto, tra cui la sottocartella images contenente le immagini senza distorsione della fotocamera. Finito il processo, nella finestra 'Dense reconstruction' comparirà la lista delle immagini elaborate.
   
   .. image:: assets/dsm/images/colmap_dense_reconstruction.png

4. Premere [Options]. Nel tab Stereo, aumentare il parametro max_image_size per aumentare il numero di punti della nuvola finale. Chiudere la finestra 'Dense reconstruction options'.
   
   .. image:: assets/dsm/images/colmap_options.png

5. Premere [Stereo]. Questo processo è molto lento.

6. Premere [Fusion]. Nella cartella progetto si creerà il file fused.ply contenente la nuvola di punti.

Step 4: ritaglio e georeferenziazione della nuvola di punti con CloudCompare
----------------------------------------------------------------------------
1. Aprire CloudCompare e la nuvola di punti creata alla fine dello step 3 tramite File > Open... Si aprirà la finestra Ply File Open. Premere [Apply all] lasciando tutto come default. Aspettare che CloudCompare carichi la nuvola di punti.
   
   .. image:: assets/dsm/images/cc.png

2. Nel 'DB Tree', selezionare la nuvola e ritagliare la zona di interesse con lo strumento Edit > Segment.
   
   .. image:: assets/dsm/images/cc_segment.png

3. Per la georeferenziazione, selezionare di nuovo la nuvola nel 'DB Tree' e premere Tools > Registration > Align (point pairs picking). `Video di Paolo Corradeghini sulla georeferenziazione in CloudCompare`_.
   
   NB: ricordarsi il sistema di riferimento usato.

   .. image:: assets/dsm/images/cc_align.png

Step 5: calcolo della distanza media tra i punti

Step 6: Creazione del raster DSM

.. _sito ufficiale di colmap: https://github.com/colmap/colmap/releases
.. _sito ufficiale di CloudCompare: https://www.cloudcompare.org/
.. _Video di Paolo Corradeghini sulla georeferenziazione in CloudCompare: https://www.youtube.com/watch?v=YsGNoCPR9K4