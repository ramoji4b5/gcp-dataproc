**create dataproc cluster**
> gcloud dataproc clusters create cluster-name \
    --region=region

############# create cluster with knox gate way###############
gcloud dataproc clusters create cluster-name \
    --enable-component-gateway \
    --region=region \
    other args ...

############# optional components#################### ###
gcloud dataproc clusters create cluster-name \
  --optional-components=COMPONENT-NAME(s) \
  ... other flags

############config machine####################
gcloud dataproc clusters create test-cluster /
    --master-machine-type custom-6-23040 /
    --worker-machine-type custom-6-23040 /
    other args
#####################
gcloud dataproc clusters create cluster-name \
    --region=region \
    --metadata=name1=value1,name2=value2... \
    ... other flags ...

###############final_cmd###################################
gcloud dataproc clusters create nssl-dataproc --enable-component-gateway --bucket nssl-data-stage --region asia-south1 --zone asia-south1-c --master-machine-type n1-standard-4 --master-boot-disk-size 200 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 200 --image-version 2.0-debian10 --optional-components HIVE_WEBHCAT,JUPYTER --scopes 'https://www.googleapis.com/auth/cloud-platform' --project zelar-data-hadoop
#################3cluster creation##############################################
gcloud dataproc clusters create nssl-data-proc --enable-component-gateway --bucket nssl-data-stage-us --region us-central1 --zone us-central1-a --master-machine-type n1-standard-4 --master-boot-disk-size 200 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 200 --image-version 2.0-debian10 --optional-components HIVE_WEBHCAT,JUPYTER --scopes 'https://www.googleapis.com/auth/cloud-platform' --project zelar-data-hadoop
##############deletion#########################################################
gcloud dataproc clusters delete nssl-data-proc --region=us-central1


##################configure Ranger#############################################
gcloud projects add-iam-policy-binding zelar-data-hadoop \
    --member=serviceAccount:zelar-data-hadoop-comput\
    --role=roles/cloudkms.cryptoKeyDecrypter

gcloud iam service-accounts create zelar-data-hadoop-compute \
    --description="NSSL Service Account" \
    --display-name="nssl_service_account"

gcloud projects add-iam-policy-binding zelar-data-hadoop --member=serviceAccount:zelar-data-hadoop-compute@zelar-data-hadoop.iam.gserviceaccount.com --role=roles/cloudkms.cryptoKeyDecrypter roles/dataproc.admin
    
gcloud kms keyrings create nssl-keyring --location=global
gcloud kms keys create nssl-key \
    --location=global \
    --keyring=nssl-keyring \
    --purpose=encryption

echo "admin@123" | \
  gcloud kms encrypt \
    --location=global \
    --keyring=nssl-keyring \
    --key=nssl-key \
    --plaintext-file=- \
    --ciphertext-file=admin-password.encrypted

gsutil cp admin-password.encrypted gs://nssl-data-stage-us 

gcloud dataproc clusters create nssl-data-proc --enable-component-gateway --bucket nssl-data-stage-us --region us-central1 --zone us-central1-a --service-account zelar-data-hadoop-compute@zelar-data-hadoop.iam.gserviceaccount.com --master-machine-type n1-standard-4 --master-boot-disk-size 200 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 200 --image-version 2.0-debian10 --optional-components HIVE_WEBHCAT,JUPYTER,RANGER,SOLR  --properties="dataproc:ranger.kms.key.uri=projects/project-id/locations/global/keyRings/nssl-keyring/cryptoKeys/nssl-key,dataproc:ranger.admin.password.uri=gs://nssl-data-stage-us/admin-password.encrypted" --scopes 'https://www.googleapis.com/auth/cloud-platform' --project zelar-data-hadoop

zelar-data-hadoop-compute@zelar-data-hadoop.iam.gserviceaccount.com


gcloud dataproc clusters create cluster-name \
    --optional-components=SOLR,RANGER \
    --region=region \
    --enable-component-gateway \
    --properties="dataproc:ranger.kms.key.uri=projects/project-id/locations/global/keyRings/my-keyring/cryptoKeys/my-key,dataproc:ranger.admin.password.uri=gs://my-bucket/admin-password.encrypted" \
    ... other flags


gcloud dataproc clusters create nssl-data-proc \
    --region=region \
    --service-account=service-account-name@project-id.iam.gserviceaccount.com \
    --scopes=scope[, ...]    

reference URL
    https://cloud.google.com/architecture/hadoop/connecting-visualization-software-to-hadoop-on-google-cloud
    https://github.com/GoogleCloudPlatform/dataproc-connect-visualization/tree/master/terraform

**git help document**
[gits page for git hub help](https://gist.github.com/ramoji4b5/0b7fc7f3cf0fe504b2068025ed081f69)