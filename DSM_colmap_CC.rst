Creare DSM con COLMAP e CloudCompare
====================================

Step 1: Preparazione della cartella progetto e dei programmi
------------------------------------------------------------

1. Creare come cartella progetto una cartella vuota e posizionare le foto stereo di interesse in una sua sottocartella.
2. Scaricare dal `sito ufficiale di COLMAP`_ il programma.
   
   NB: COLMAP necessita di una scheda video NVIDIA che supporti CUDA.
3. Scaricare dal `sito ufficiale di CloudCompare`_ il programma ed installarlo.

Step 2: Ricostruzione sparsa della nuvola di punti con COLMAP
-------------------------------------------------------------

1. Estrarre la cartella compressa di COLMAP.
2. Avviare il file batch 'COLMAP.bat'. Si aprirà una finestra del terminale e dopo qualche secondo l'interfaccia grafica di COLMAP (al primo avvio ci metterà un po\').
   
   .. image:: assets/dsm/images/colmap.png

3. Creare un nuovo progetto premendo su File > New project. Si aprirà una finestrella che ci chiederà un database e le foto stereo. 

   * Per il database: premere [New], navigare all'interno della cartella progetto creata nello Step 1 e creare un nuovo database.
   * Per le immagini: premere [Select] e selezionare la sottocartella contenente le foto stereo.
   * Premere [Save].
  
   .. image:: assets/dsm/images/colmap_new_project.png

4. Premere File > Save project. Salvare il file .ini all'interno della cartella progetto e ricordarsi di salvare dopo ogni prossimo passaggio di questo step.

5. Premere Processing > Feature extraction:
   
   * Spuntare l'opzione 'Shared for all images'. In questo modo i parametri intrinseci inziali della camera saranno considerati uguali per tutte le immagini.
   * Scegliere 'Custom parameters' e scrivere i parametri intrinseci della camera. L'unità di misura deve essere in pixel. 
     
      Esempio: in COLMAP è stato scelto SIMPLE RADIAL come modello di camera per elaborare immagini di dimensioni 31520x13440. Sono di conseguenza necessari quattro parametri intrinseci:
         1. focal length (f);
         2. principal point x (cx);
         3. principal point y (cy);
         4. radial distortion (k);
   
      Nel certificato di calibrazione della camera si leggono i seguenti valori:
       
       .. image:: assets/dsm/images/colmap_intrinsics.png
      
      I parametri da scrivere dentro COLMAP saranno quindi:
         
         1.  f = (108.123 mm) / (0.00376 mm/px) =  28756.117 px

         2. cx = (width dell'immagine / 2) + (x / pixel size) =

             = (31520 px / 2) + (-0.06992 mm / (0.00376 mm/px)) =

             = 15760 px - 18.596 px = 15741.404 px
         
         3. cy = (height dell'immagine / 2) + (y / pixel size) = 
            
             = (13440 px / 2) + (-0.05048 mm / (0.00376 mm/px)) = 

             = 6720 px - 13.426 px = 6706.574
         
         4.  k ≈ 0
      
      NB: COLMAP utilizza questi parametri come valori iniziali, che verrato migliorati iterativamente durante il processo di ricostruzione sparsa. Di conseguenza non è necessario che siano perfetti.

      .. image:: assets/dsm/images/colmap_feature_extraction.png

   * Aumentare il numero di features da estrarre da ogni immagine aumentando il parametro sift.max_num_features (valore consigliato per la workstation: 45000). 
   * Premere [Extract].

6. Chiudere la finestra di Feature extraction e premere Processing > Feature matching. Aumentare il numero di accoppiamenti tra le features delle immagini aumentando il parametro max_num_matches (valore consigliato per la workstation: 45000). Premere [Run].
   
   .. image:: assets/dsm/images/colmap_feature_matching.png

7. Chiudere la finestra di Feature matching e premere Reconstruction > Start Reconstruction.
   
   .. image:: assets/dsm/images/colmap_sparse_reconstruction.png

Step 3: Ricostruzione densa della nuvola di punti con COLMAP
------------------------------------------------------------

1. Premere Reconstruction > Dense reconstruction. Si aprirà una nuova finestra.
2. Premere [Select] e selezionare la cartella progetto.
3. Premere [Undistortion]. Si creeranno diversi nuovi file e sottocartelle all'interno della cartella progetto, tra cui la sottocartella images contenente le immagini senza distorsione della fotocamera. Finito il processo, nella finestra 'Dense reconstruction' comparirà la lista delle immagini elaborate.
   
   .. image:: assets/dsm/images/colmap_dense_reconstruction.png

4. Premere [Options]. Nel tab Stereo, aumentare il parametro max_image_size per aumentare il numero di punti della nuvola finale (valore consigliato per la workstation: 10000). Chiudere la finestra 'Dense reconstruction options'.
   
   .. image:: assets/dsm/images/colmap_options.png

5. Premere [Stereo]. Questo processo è molto lento.

6. Premere [Fusion]. Nella cartella progetto si creerà il file fused.ply contenente la nuvola di punti.

Step 4: Ritaglio e georeferenziazione con CloudCompare
----------------------------------------------------------------------------

1. Aprire CloudCompare e la nuvola di punti creata alla fine dello step 3 tramite File > Open... Si aprirà la finestra Ply File Open. Premere [Apply all] lasciando tutto come default. Aspettare che CloudCompare carichi la nuvola di punti.
   
   .. image:: assets/dsm/images/cc.png

2. Nel 'DB Tree', selezionare la nuvola e ritagliare la zona di interesse con lo strumento Edit > Segment.
   
   .. image:: assets/dsm/images/cc_segment.png

3. Per la georeferenziazione, selezionare di nuovo la nuvola nel 'DB Tree' e premere Tools > Registration > Align (point pairs picking). `Video di Paolo Corradeghini sulla georeferenziazione in CloudCompare`_.
   
   NB: ricordarsi il sistema di riferimento usato.

   .. image:: assets/dsm/images/cc_align.png

Step 5: Calcolo della distanza media tra i punti in CloudCompare
----------------------------------------------------------------

1. Selezionare la nuvola nel 'DB Tree' e premere Tools > Other > Compute geometric features. Si aprirà la finestra 'Geometric features'.
2. Spuntare il parametro 'Surface density' e aumentare il 'Local neighborhood radius' a 5.0. Premere OK e aspettare qualche secondo. Alla fine del processo verrà creato un nuovo scalar field della nuvola rappresentante la densità di superficie calcolata in ogni punto considerando un raggio pari al 'Local neighborhood radius'.
   
   .. image:: assets/dsm/images/cc_geometric_features.png

3. Per ottenere la densità di superficie media, premere Edit > Scalar fields > Compute stat. params. Scegliere Weibull nella finestra 'Distribution Fitting' e premere [OK].

   .. image:: assets/dsm/images/cc_distribution_fitting.png

   La densità di superficie media verrà stampata nella console di CloudCompare come 'Average value'.

   .. image:: assets/dsm/images/cc_scalar_field_stats.png

4. Calcolare 'a mano' la distanza media tra i punti = 1/sqrt(densità di superficie media).
   
   Nel nostro caso, distanza media = 1/sqrt(0.727761) = 1.37 m

Step 6: Creazione del raster DSM in CloudCompare
------------------------------------------------

1. Premere Tools > Projection > Rasterize (and contour plot). Si aprirà la finestra 'Rasterize'. Cambiare i seguenti parametri come descritto:
   
   * Per il parametro 'step', scegliere un valore superiore alla distanza media tra i punti, calcolata alla fine dello Step 5. Questo valore determina la grandezza dei pixel nel raster finale.
   * Per il parametro 'direction', scegliere l'asse Z.
   * Sotto 'Empty cells', per il parametro 'Fill with' scegliere 'kriging'.
   * Lasciare gli altri parametri con il loro valore di default.

2. Premere [Update grid].
   
   .. image:: assets/dsm/images/cc_rasterize.png

3. Premere [Raster]. Si aprirà la finestra 'raster export options'. Lasciare come opzione 'export heights' e premere [OK]. Scegliere dove salvare il file GeoTIFF. 
   
   .. image:: assets/dsm/images/cc_raster_export_options.png

Step 7: Conclusioni
-------------------

1. Salvare la nuvola di punti come .e57 oppure come .las. Il file .las può essere aperto da più programmi ma non è del tutto fedele alla nuvola originale.
2. Aprire in QGIS il raster salvato e impostare il suo sistema di riferimento a quello utilizzato per la georeferenziazione nello Step 4, ed esportare di nuovo il raster.

   .. image:: assets/dsm/images/qgis_raster.png

.. _sito ufficiale di COLMAP: https://github.com/colmap/colmap/releases
.. _sito ufficiale di CloudCompare: https://www.cloudcompare.org/
.. _Video di Paolo Corradeghini sulla georeferenziazione in CloudCompare: https://www.youtube.com/watch?v=YsGNoCPR9K4