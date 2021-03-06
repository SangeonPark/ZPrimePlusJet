Instructions to run create cards and run limits, etc.

Create templates:
```
# create all templates, with passing region double b-tag > 0.9
export SKLIMDIR=/eos/uscms/store/user/lpchbb/zprimebits-v12.04/cvernier
python Hbb_create.py -i root://cmseos.fnal.gov/$SKLIMDIR -o output-miniaod-pfmet140-hptckkw-hqq125ptShape --lumi 35.9

# create templates for muon CR
python Hbb_create.py -i root://cmseos.fnal.gov/$SKLIMDIR -o output-miniaod-pfmet140-hptckkw-hqq125ptShape --lumi 35.9 -m

# create looser templates, with passing region double b-tag > 0.8 
# (have to change hard-coded value DBTAGCUT = 0.8 in sampleContainer.py)
python Hbb_create.py -i root://cmseos.fnal.gov/$SKLIMDIR -o output-miniaod-pfmet140-hptckkw-hqq125ptShape-looser --lumi 35.9 --skip-qcd --skip-data
cp output-miniaod-pfmet140-hptckkw-hqq125ptShape-looser/hist_1DZbb_pt_scalesmear.root output-miniaod-pfmet140-hptckkw-hqq125ptShape/hist_1DZbb_pt_scalesmear_looserWZ.root
```

Make workspaces and datacards:
```
# make workspaces
python buildRhalphabetHbb.py -i output-miniaod-pfmet140-hptckkw-hqq125ptShape/hist_1DZbb_pt_scalesmear.root --ifile-loose output-miniaod-pfmet140-hptckkw-hqq125ptShape/hist_1DZbb_pt_scalesmear_looserWZ.root -o output-miniaod-pfmet140-hqq125ptShape/ --remove-unmatched  --prefit --addHptShape

# make datacards
python makeCardsHbb.py -i output-miniaod-pfmet140-hqq125ptShape/hist_1DZbb_pt_scalesmear.root --ifile-loose output-miniaod-pfmet140-hqq125ptShape/hist_1DZbb_pt_scalesmear_looserWZ.root -o output-miniaod-pfmet140-hqq125ptShape/ --remove-unmatched --no-mcstat-shape

# make muonCR datacards
python writeMuonCRDatacard.py -i output-miniaod-pfmet140-hqq125ptShape/  -o output-miniaod-pfmet140-hqq125ptShape/
cd output-miniaod-pfmet140-hqq125ptShape/
```

Run combine commands:
```
# combine cards
combineCards.py cat1=card_rhalphabet_cat1.txt cat2=card_rhalphabet_cat2.txt  cat3=card_rhalphabet_cat3.txt cat4=card_rhalphabet_cat4.txt  cat5=card_rhalphabet_cat5.txt cat6=card_rhalphabet_cat6.txt muonCR=datacard_muonCR.txt > card_rhalphabet_muonCR.txt

# convert to binary file
text2workspace.py -P HiggsAnalysis.CombinedLimit.PhysicsModel:multiSignalModel -m 125  --PO verbose --PO 'map=.*/*hqq125:r[1,0,20]' --PO 'map=.*/zqq:r_z[1,0,20]' card_rhalphabet_muonCR.txt -o card_rhalphabet_muonCR_floatZ.root
```

Follow other combine instructions:
https://github.com/DAZSLE/ZPrimePlusJet/blob/Hbb_Diff-pT/fitting/PbbJet/README.md
