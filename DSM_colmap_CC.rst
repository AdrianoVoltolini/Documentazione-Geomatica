Creare DSM con colmap e CloudCompare
====================================

Step 1: Preparazione della cartella progetto e dei programmi
------------------------------------------------------------

1. Creare come cartella progetto una cartella vuota e posizionare le foto stereo di interesse in una sua sottocartella.
2. Scaricare dal `sito ufficiale di colmap`_ il programma.
   
   NB: colmap necessita di una scheda video NVIDIA installata nel PC per funzionare. 
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
3. Premere [Undistortion]. Si creeranno diversi nuovi file e sottocartelle all'interno della cartella progetto, tra cui la sottocartella images contenente le immagini senza distorsione della fotocamera. Finito il processo, nella finestra della Dense reconstruction comparirà la lista delle immagini elaborate.
   
   .. image:: assets/dsm/images/colmap_dense_reconstruction.png

4. Premere [Options]. Nel tab stereo, aumentare il parametro max_image_size. 

.. _sito ufficiale di colmap: https://github.com/colmap/colmap/releases
.. _sito ufficiale di CloudCompare: https://www.cloudcompare.org/