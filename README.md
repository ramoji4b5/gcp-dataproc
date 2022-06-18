##############create dataproc cluster####################
gcloud dataproc clusters create cluster-name \
    --region=region

############# create cluster with knox gate way###############
gcloud dataproc clusters create cluster-name \
    --enable-component-gateway \
    --region=region \
    other args ...

############# optional components#######################
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
gcloud dataproc clusters create nssl-dataproc --enable-component-gateway --bucket nssl-data-stage --region asia-south1 --zone asia-south1-a --master-machine-type n1-standard-4 --master-boot-disk-size 200 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 200 --image-version 2.0-debian10 --optional-components HIVE_WEBHCAT,JUPYTER --scopes 'https://www.googleapis.com/auth/cloud-platform' --project zelar-data-hadoop

