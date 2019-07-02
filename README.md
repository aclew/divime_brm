# divime_brm
divime description journal paper

# To generate pyannote metrics
# conda create --name pyannote-lena python=3.6 #only needed once
source activate pyannote-lena 
# pip install pyannote-metrics #only needed once

# organize your rttms such that lines are ordered in increasing time

for j in yunres/*.rttm ; do mv $j $j.unsorted ; done
for j in yunres/*.rttm.unsorted ; do sorted=`echo $j | sed "s/.unsorted//"` ; sort -n -k4 $j > $sorted ; done

for j in gold/*.rttm ; do mv $j $j.unsorted ; done
for j in gold/*.rttm.unsorted ; do sorted=`echo $j | sed "s/.unsorted//"` ; sort -n -k4 $j > $sorted ; done

for j in yunnew/*.rttm ; do mv $j $j.unsorted ; done
for j in yunnew/*.rttm.unsorted ; do sorted=`echo $j | sed "s/.unsorted//"` ; sort -n -k4 $j > $sorted ; done


## Map the labels
## You can change the mapping in labels_mapper.py
## The -o option indicates if overlapping classes need to be aggregated as being "OVL"

python labels_mapper.py -p yunres/ -m gold
python labels_mapper.py -p yunnew/ -m gold
python labels_mapper.py -p gold/ -m gold -o

## Evaluate lena performances (should not be run in the frontnode)
# With -t being the task (identification, diarization or detection)
# -m being the list of metrics
python compute_metrics.py -ref gold/mapped/ -hyp yunnew/mapped/ -t diarization -m diaer coverage homogeneity completeness purity
