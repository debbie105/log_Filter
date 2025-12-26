#!/usr/bin/env perl

use strict;
use warnings;


mkdir("./LogGrep");
mkdir("./result");

my @files = glob("*.log");
foreach my $i (@files){
  my $filename = $i;
  $filename =~ s/\.log$/_OasGrep\.log/;
#  system("grep \"ERROR\\|SEQU\\|AGA\\|IPCOM\\|ATUN\\|AAM\\|AAC\\|MarkPitch\\|IPLOG\\|PAOP\\|SEQU  :1400:ULotID\\|SEQU  :6975:ProcessJobStart: Recipe Name\\|WSSTEP:321447\" $i > ./LogGrep/$filename");
  system("grep \"ERROR\\|SEQU\\|AGA\\|IPCOM\\|ATUN\\|AAM\\|AAC\\|MarkPitch\\|IPLOG\\|PAOP\\|SEQU  :1400:ULotID\\|SEQU  :6975:ProcessJobStart: Recipe Name\\|WSSTEP:321447\\|HOAM  :7130\" $i > ./LogGrep/$filename");
}

chdir "./LogGrep";
my @grepfiles = glob("*.log");
chdir "../";

chdir "./result";
system("rm -f *.*");
chdir "../";

foreach my $i (@grepfiles){
  my ($LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo);                                 #基本条件
  my ($KindOfMeas,$il,$MarkType,$MarkType_rsa,$MeasDir,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime,$iris,$Algorism);     #計測条件
  my ($MarkType_tv,$ilmode_tv,$nd1_tv,$nd2_tv,$nd3_tv,$hlvol_tv,$ctime_tv,$iris_tv,$lightlevel_tv);         #計測条件
  my ($wsx,$wsy,$wsz,$wsqx,$wsqy,$wsq);                                                        #計測条件
  my ($wsx_tv,$wsy_tv,$wsz_tv,$wsqx_tv,$wsqy_tv,$wsq_tv);                                      #計測条件
  my ($wsx_rsa,$wsy_rsa,$wsz_rsa,$wsqx_rsa,$wsqy_rsa,$wsq_rsa);                                #計測条件
  my ($wsx_hrd,$wsy_hrd,$wsz_hrd,$wsqx_hrd,$wsqy_hrd,$wsq_hrd);                                #計測条件
  my ($posx,$posy,$posz,$posqx,$posqy,$posq);                                                  #計測条件
  my ($posx_tv,$posy_tv,$posz_tv,$posqx_tv,$posqy_tv,$posq_tv);                                #計測条件
  my ($posx_rsa,$posy_rsa,$posz_rsa,$posqx_rsa,$posqy_rsa,$posq_rsa);                          #計測条件
  my ($posx_hrd,$posy_hrd,$posz_hrd,$posqx_hrd,$posqy_hrd,$posq_hrd);                          #計測条件
  my ($ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$wev_x_mean,$sigma_X,$uneven_X,$profile_X,$wev_y_mean,$sigma_Y,$uneven_Y,$profile_Y,$brightness_X,$brightness_Y);       #評価結果
  my ($IP_ERROR);                                                                               #評価結果
  my ($OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ);                                                         #評価結果
  my ($Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY);       #評価結果
  my ($light_val_X,$light_val_Y,$light_tol_min,$light_tol_max);                                               #評価結果
  
  $LotID = $recipe = $time = $TagID1 = $TagID2 = $WaferNo = $ShotNo = "--";
  $KindOfMeas = $il = $MarkType = $MarkType_rsa = $MeasDir = $ilmode = $nd1 = $nd2 = $nd3 = $nd4 = $hlvol = $ctime = $iris = $Algorism = "--";
  $MarkType_tv = $ilmode_tv = $nd1_tv = $nd2_tv = $nd3_tv = $hlvol_tv = $ctime_tv = $iris_tv = $lightlevel_tv = "--";
  $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
  $wsx_tv = $wsy_tv = $wsz_tv = $wsqx_tv = $wsqy_tv = $wsq_tv = "--";
  $wsx_rsa = $wsy_rsa = $wsz_rsa = $wsqx_rsa = $wsqy_rsa = $wsq_rsa = "--";
  $wsx_hrd = $wsy_hrd = $wsz_hrd = $wsqx_hrd = $wsqy_hrd = $wsq_hrd = "--";
  $posx = $posy = $posz = $posqx = $posqy = $posq = "--";
  $posx_tv = $posy_tv = $posz_tv = $posqx_tv = $posqy_tv = $posq_tv = "--";
  $posx_rsa = $posy_rsa = $posz_rsa = $posqx_rsa = $posqy_rsa = $posq_rsa = "--";
  $posx_hrd = $posy_hrd = $posz_hrd = $posqx_hrd = $posqy_hrd = $posq_hrd = "--";
  $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
  $IP_ERROR = "--";
  $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
  $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
  $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
  
  open(FILE,"./LogGrep/$i") or die("Can't open the file\n");
  my $filename = $i;
  $filename =~ s/\_OasGrep.log$/_OasLog\.txt/;
  open(NewFILE, ">","./result/$filename");
  print NewFILE "LotID,recipe,time,TagID1,TagID2,WaferNo,ShotNo,KindOfMeas,il,MarkType,ilmode,nd1/ndc,nd2/ndf_m,nd3/ndf_s1,ndf_s2,hlvol,ctime,iris,algorism,IP_ERROR,ErrPix_X,ErrPix_Y,ContrastX,ContrastY,Correlation(TVPA/RSA),MeasDir,lightlevel_tv,wsx,wsy,wsz,wsqx,wsqy,wsq,posx,posy,posq,OcRawX,OcRawY,OcCompdX,OcCompdY,OcCompdZ,Mean_X,Mean_Y,sigma_X,sigma_Y,uneven_X,uneven_Y,profileX,profileY,CorrelationX,CorrelationY,DifSlopeX,DifSlopeY,light_val_X,light_val_Y,light_tol_min,light_tol_max\n";
  print "$i\n";
  while(my $line = <FILE>){
    $line =~ s/\x0D?\x0A?$//;     #改行文字削除
    
    
    if($line =~ /SEQU  :1400:ULotID:/){
      my @list = split(/:/, $line);
      $LotID = $list[5];
      next;
    }
    if($line =~ /SEQU  :6975:ProcessJobStart/){
      my @list = split(/:/, $line);
      $recipe = $list[6];
      next;
    }
    
#    if($line =~ /SEQU  : 498:/){
#      my @list = split(/(?:\+|\")/, $line);
#      $WaferNo = $list[2];
#      next;
#    }
    if($line =~ /ATUN  :1060/){
      $time = $TagID1 = $TagID2 = $WaferNo = $ShotNo = $KindOfMeas = $il = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $MeasDir = "--";
      my @list = split(/(?: |\]|\+|:)/, $line);
      $WaferNo = $list[11];
      $ShotNo = $list[18];
      $il = $list[14];
      next;
    }
    if($line =~ /SEQU  :  33:SeqNo:0067 message:0067: Auto TCIP Measurement/){
      $time = $TagID1 = $TagID2  = $KindOfMeas = $il = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $MeasDir = "--";
      $ShotNo = "SRM";
      next;
    }
    
    ##############WS位置###################
    if($line =~ /WSSTEP:321447/){  
      my @list = split(/(?: |:)/, $line);
      $wsx  = $list[17];
      $wsy  = $list[19];
      $wsz  = $list[21];
      $wsqx = $list[23];
      $wsqy = $list[25];
      $wsq  = $list[27];
    }
    
    ##############計測位置(Wafer座標)###################
    if($line =~ /AAC   : 767:/){  
      my @list = split(/(?: |=)/, $line);
      $posx  = $list[13];
      $posy  = $list[15];
      $posq  = $list[17];
    }
    
    ##############照明モード設定####################
    if($line =~ /AAC   : 620:AaCompoObserveOas/){
      my @list = split(/(?: |\]|\+|=)/, $line);
      $il= $list[12];
      next;
    }
    
    if($line =~ /AAC   : 548:Send Charge Time to/){  #TVPAの蓄積時間
      my @list = split(/:/, $line);
      $ctime_tv  = $list[5];
      next;
    }
    
    if($line =~ /AAC   : 106:LightControl UnitPos/){  #通常OAS,TV
      my @list = split(/(?: |:)/, $line);
      if ($list[27] eq "HOAS"){
        $ilmode = $list[15];
        $nd1    = $list[17];
        $nd2    = $list[19];
        $nd3    = $list[21];
        $nd4    = "--";
        $hlvol  = $list[23];
        $ctime  = $list[25];
        $iris   = $list[27];
      }else{
#        $ilmode_tv = $list[15];
        $nd1_tv    = $list[17];
        $nd2_tv    = $list[19];
        $nd3_tv   = $list[21];
        $nd4    = "--";
        $hlvol_tv  = $list[23];
        $ctime_tv  = $list[25];
        $iris_tv   = $list[27];
      }
      next;
    }
    if($line =~ /AAC   :8100:/){  #NOAS,TV
      my @list = split(/(?: |:)/, $line);
      $ilmode_tv = $list[15];
    }
    if($line =~ /AAC   :7060:/){  #通常OAS,TV
      my @list = split(/(?: |:)/, $line);
      $ilmode_tv = $list[15];
    }
    if($line =~ /AAC   :2132:AaCompoOffsetCheckOasS/){  #NOAS,HRD
      my @list = split(/(?: |:)/, $line);
#      $ilmode = $list[9];
        $MarkType = $list[16];
    }
    
    
    if($line =~ /AAC   :8099:LightControl UnitPos.:/){  #NOAS
      my @list = split(/(?: |:)/, $line);
      $ilmode = $list[13];
      $nd1    = $list[15];
      $nd2    = $list[17];
      $nd3    = $list[19];
      $nd4    = $list[21];
      $hlvol  = $list[23];
      $ctime  = $list[25];
      $iris   = $list[27];
      next;
    }
    
    
    ##############TVPA計測####################
    if($line =~ /712:AaCompoOffsetCheckLoasS/){                         #TVPA計測設定
      my @list2 = split(/(?:=|,)/, $line);
      $MarkType_tv = $list2[2];
      next;
    }
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 19 \[ 1\] 2020 \[ 2\] 3/){     #TVPA計測開始(光量計測？)
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $TagID1 = $list2[24];
      $KindOfMeas = "TVPA_INT";
      $wsx_tv  = $wsx;
      $wsy_tv  = $wsy;
      $wsz_tv  = $wsz;
      $wsqx_tv = $wsqx;
      $wsqy_tv = $wsqy;
      $wsq_tv  = $wsq;
      $posx_tv  = $posx;
      $posy_tv  = $posy;
      $posq_tv  = $posq;
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 12 \[ 1\] 2020 \[ 2\] 3/){     #TVPA計測終了(光量計測？)
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode_tv,$nd1_tv,$nd2_tv,$nd3_tv,$nd4,$hlvol_tv,$ctime_tv,$iris_tv,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_tv,$wsy_tv,$wsz_tv,$wsqx_tv,$wsqy_tv,$wsq_tv,$posx_tv,$posy_tv,$posq_tv,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      next;
    }
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 25 \[ 1\] 2020 \[ 2\] 4/){     #TVPAずれ量計測開始
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $KindOfMeas = "TVPA_POS";
      $TagID1 = $list2[24];
      $wsx_tv  = $wsx;
      $wsy_tv  = $wsy;
      $wsz_tv  = $wsz;
      $wsqx_tv = $wsqx;
      $wsqy_tv = $wsqy;
      $wsq_tv  = $wsq;
      $posx_tv  = $posx;
      $posy_tv  = $posy;
      $posq_tv  = $posq;
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 14 \[ 1\] 2020 \[ 2\] 4/){     #TVPAずれ量計測完了
      my @list = split(/(?: |\]|\+)/, $line);
      $IP_ERROR = $list[20];
      $ErrPix_X = $list[40];
      $ErrPix_Y = $list[44];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode_tv,$nd1_tv,$nd2_tv,$nd3_tv,$nd4,$hlvol_tv,$ctime_tv,$iris_tv,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_tv,$wsy_tv,$wsz_tv,$wsqx_tv,$wsqy_tv,$wsq_tv,$posx_tv,$posy_tv,$posq_tv,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 54 \[ 1\] 2020 \[ 2\] 4/){     #TVPAずれ量計測完了??
      my @list = split(/(?: |\]|\+)/, $line);
      $IP_ERROR = $list[20];
      $ErrPix_X = $list[40];
      $ErrPix_Y = $list[44];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode_tv,$nd1_tv,$nd2_tv,$nd3_tv,$nd4,$hlvol_tv,$ctime_tv,$iris_tv,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_tv,$wsy_tv,$wsz_tv,$wsqx_tv,$wsqy_tv,$wsq_tv,$posx_tv,$posy_tv,$posq_tv,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      next;
    }
    if($line =~ /AAC   : 717:/){                                                #TVPA計測評価
      my @list = split(/(?:\]|\+|=|,)/, $line);
      $Correlation = $list[9];
      $lightlevel_tv = $list[21];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode_tv,$nd1_tv,$nd2_tv,$nd3_tv,$nd4,$hlvol_tv,$ctime_tv,$iris_tv,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,$lightlevel_tv,$wsx_tv,$wsy_tv,$wsz_tv,$wsqx_tv,$wsqy_tv,$wsq_tv,$posx_tv,$posy_tv,$posq_tv,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      $time = $TagID1 = $TagID2 = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
      $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $IP_ERROR = "--";
      $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
      $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
      $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
      next;
    }
    ##############TVPA計測####################
    
    ##############RSA計測####################
    if($line =~ /AAC   : 749:AaCompoOffsetCheckMoasS/){                         #TVPA計測設定
      my @list2 = split(/(?:=|,)/, $line);
      $MarkType_tv = $list2[4];
      next;
    }
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 27 \[ 1\] 2022 \[ 2\] 4/){     #RSAずれ量計測開始
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $TagID1 = $list2[24];
      $wsx_rsa  = $wsx;
      $wsy_rsa  = $wsy;
      $wsz_rsa  = $wsz;
      $wsqx_rsa = $wsqx;
      $wsqy_rsa = $wsqy;
      $wsq_rsa  = $wsq;
      $posx_rsa  = $posx;
      $posy_rsa  = $posy;
      $posq_rsa  = $posq;
      $KindOfMeas = "RSA";
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 14 \[ 1\] 2022 \[ 2\] 4/){     #RSAずれ量計測完了
      my @list = split(/(?: |\]|\+)/, $line);
      $IP_ERROR = $list[20];
      $ErrPix_X = $list[40];
      $ErrPix_Y = $list[44];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime_tv,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,$lightlevel_tv,$wsx_rsa,$wsy_rsa,$wsz_rsa,$wsqx_rsa,$wsqy_rsa,$wsq_rsa,$posx_rsa,$posy_rsa,$posq_rsa,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      next;
    }
    if($line =~ /AAC   : 752:/){                                                #RSA計測評価
      my @list = split(/(?:\]|\+|=|,)/, $line);
      $Correlation = $list[5];
      $lightlevel_tv = $list[15];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType_tv,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime_tv,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,$lightlevel_tv,$wsx_rsa,$wsy_rsa,$wsz_rsa,$wsqx_rsa,$wsqy_rsa,$wsq_rsa,$posx_rsa,$posy_rsa,$posq_rsa,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      $time = $TagID1 = $TagID2 = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
      $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $IP_ERROR = "--";
      $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
      $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
      $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
      next;
    }
    ##############RSA計測####################
    
    ##############HRD計測####################
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 40 \[ 1\] 2024 \[ 2\] 8/){     #HRDずれ量計測開始
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $TagID1 = $list2[24];
      $KindOfMeas = "HRD_MEAS";
      $Algorism = $list2[86];
      $MeasDir = $list2[74];            #HRD計測時は、引数に計測方向が入る。
      $wsx_hrd  = $wsx;
      $wsy_hrd  = $wsy;
      $wsz_hrd  = $wsz;
      $wsqx_hrd = $wsqx;
      $wsqy_hrd = $wsqy;
      $wsq_hrd  = $wsq;
      $posx_hrd  = $posx;
      $posy_hrd  = $posy;
      $posq_hrd  = $posq;
      next;
    }
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 28 \[ 1\] 2024 \[ 2\] 8/){     #HRDずれ量計測開始→バージョンが古いとこっちになる？
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $TagID1 = $list2[24];
      $KindOfMeas = "HRD_MEAS";
      $MeasDir = $list2[74];            #HRD計測時は、引数に計測方向が入る。
      $wsx_hrd  = $wsx;
      $wsy_hrd  = $wsy;
      $wsz_hrd  = $wsz;
      $wsqx_hrd = $wsqx;
      $wsqy_hrd = $wsqy;
      $wsq_hrd  = $wsq;
      $posx_hrd  = $posx;
      $posy_hrd  = $posy;
      $posq_hrd  = $posq;
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 48 \[ 1\] 2024 \[ 2\] 8/){     #HRDずれ量計測完了
      my @list = split(/(?: |\]|\+)/, $line);
      if($MeasDir eq "1"){                  #1の場合はY計測
        $IP_ERROR = $list[20];
        $ErrPix_Y = $list[47];
        $ErrPix_X = "--";
      }elsif($MeasDir eq "0"){              #0の場合はY計測
        $IP_ERROR = $list[20];
        $ErrPix_X = $list[47];
        $ErrPix_Y = "--";
      }else{                                 #うまく取得できない場合は何もしない
        ;
      }
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_hrd,$wsy_hrd,$wsz_hrd,$wsqx_hrd,$wsqy_hrd,$wsq_hrd,$posx_hrd,$posy_hrd,$posq_hrd,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      $ErrPix_X = "--";
      $ErrPix_Y = "--";
      next;
    }
    if($line =~ /AAC   : 696:/){                                                #HRD計測評価
      my @list = split(/(?:=|,)/, $line);
      $OcRawX = $list[8];
      $OcRawY = $list[10];
      $OcCompdX = $list[12];
      $OcCompdY = $list[14];
      $OcCompdZ = $list[16];
      next;
    }
    if($line =~ /AAC   : 698/){                                                #HRD計測評価
      my @list = split(/(?:\]|\+|=|,)/, $line);
      $Mean_X = $list[3];
      $sigma_X = $list[5];
      $ContrastX = $list[7];
      $uneven_X = $list[9];
      $profileX = $list[11];
      next;
    }
    if($line =~ /AAC   : 699/){             #HRDずれ量計測
      my @list = split(/(?:\]|\+|=|,)/, $line);
      $Mean_Y = $list[3];
      $sigma_Y = $list[5];
      $ContrastY = $list[7];
      $uneven_Y = $list[9];
      $profileY = $list[11];
      next;
    }
    if($line =~ /AAC   :1304:AaCompoOffsetCheckOasE : X:/){             #HRDずれ量計測
      my @list = split(/(?:,|:)/, $line);
      $Correlation_X = $list[8];
      $DifSlopeX = $list[10];
      next;
    }
    if($line =~ /AAC   :1304:AaCompoOffsetCheckOasE : Y/){             #HRDずれ量計測
      my @list = split(/(?:,|:)/, $line);
      $KindOfMeas = "HRD_MEAS";
      $Correlation_Y = $list[8];
      $DifSlopeY = $list[10];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_hrd,$wsy_hrd,$wsz_hrd,$wsqx_hrd,$wsqy_hrd,$wsq_hrd,$posx_hrd,$posy_hrd,$posq_hrd,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
      $time = $TagID1 = $TagID2 = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
      $Algorism = "--";
      $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
      $IP_ERROR = "--";
      $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
      $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
      $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
      next;
    }
    if($line =~ /HOAM  :7130:/){                                        #HRD光量計測
      my @list = split(/(?: |:)/, $line);
      $KindOfMeas = "HRD_INT";
      $light_val_X = $list[16];
      $light_val_Y  = $list[18];
      $light_tol_min = $list[20];
      $light_tol_max = $list[22];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_hrd,$wsy_hrd,$wsz_hrd,$wsqx_hrd,$wsqy_hrd,$wsq_hrd,$posx_hrd,$posy_hrd,$posq_hrd,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
#      $time = $TagID1 = $TagID2 = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
#      $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
#      $Algorism = "--";
#      $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
#      $IP_ERROR = "--";
#      $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
#      $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
#      $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
      next;
    }

    ##############HRD計測####################
    
    ##############NOAS HRD計測####################
    if($line =~ /IPCOM :4244:SendParam: \[ 0\] 40 \[ 1\] 2028 \[ 2\] 8/){     #HRDずれ量計測開始
      my @list = split(/(?:\]|\+)/, $line);
      $time = $list[0];
      my @list2 = split(/(?: |\]|\+)/, $line);
      $TagID1 = $list2[24];
      $TagID2 = $list2[28];
      $Algorism = $list2[83];
      $KindOfMeas = "HRD_N";
      $MeasDir = $list2[74];            #HRD計測時は、引数に計測方向が入る。
      $wsx_hrd  = $wsx;
      $wsy_hrd  = $wsy;
      $wsz_hrd  = $wsz;
      $wsqx_hrd = $wsqx;
      $wsqy_hrd = $wsqy;
      $wsq_hrd  = $wsq;
      $posx_hrd  = $posx;
      $posy_hrd  = $posy;
      $posq_hrd  = $posq;
      next;
    }
    if($line =~ /IPCOM :4245:RecvParam: \[ 0\] 77 \[ 1\] 2028 \[ 2\] 8/){     #HRDずれ量計測完了
      my @list = split(/(?: |\]|\+)/, $line);
      $IP_ERROR = $list[20];
      $ErrPix_X = $list[50];
      $ErrPix_Y = $list[53];
      print NewFILE "$LotID,$recipe,$time,$TagID1,$TagID2,$WaferNo, $ShotNo, $KindOfMeas,$il,$MarkType,$ilmode,$nd1,$nd2,$nd3,$nd4,$hlvol,$ctime,$iris,$Algorism,$IP_ERROR,$ErrPix_X,$ErrPix_Y,$ContrastX,$ContrastY,$Correlation,$MeasDir,--,$wsx_hrd,$wsy_hrd,$wsz_hrd,$wsqx_hrd,$wsqy_hrd,$wsq_hrd,$posx_hrd,$posy_hrd,$posq_hrd,$OcRawX,$OcRawY,$OcCompdX,$OcCompdY,$OcCompdZ,$Mean_X,$Mean_Y,$sigma_X,$sigma_Y,$uneven_X,$uneven_Y,$profileX,$profileY,$Correlation_X,$Correlation_Y,$DifSlopeX,$DifSlopeY,$light_val_X,$light_val_Y,$light_tol_min,$light_tol_max\n";
#      $time = $TagID1 = $TagID2 = $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
#      $wsx = $wsy = $wsz = $wsqx = $wsqy = $wsq = "--";
#      $Algorism = "--";
#      $ErrPix_X = $ErrPix_Y = $ContrastX = $ContrastY = $Correlation = $wev_x_mean = $sigma_X = $uneven_X = $profile_X = $wev_y_mean = $sigma_Y = $uneven_Y = $profile_Y = $brightness_X = $brightness_Y = "--";
#      $IP_ERROR = "--";
#      $OcRawX = $OcRawY = $OcCompdX = $OcCompdY = $OcCompdZ = "--";
#      $Mean_X = $Mean_Y = $sigma_X = $sigma_Y = $uneven_X = $uneven_Y = $profileX = $profileY = $Correlation_X = $Correlation_Y = $DifSlopeX = $DifSlopeY = "--";
#      $light_val_X = $light_val_Y = $light_tol_min = $light_tol_max = "--";
      next;
    }

    ##############NOAS HRD計測####################
  }
  close(FILE);
  close(NewFILE);
}

chdir "./result";
system("cat *.txt > all.log");
chdir "../";
    
    
