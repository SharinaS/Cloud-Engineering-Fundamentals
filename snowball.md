# Snowball and Snowmobile

## Index - not complete

[Snowball](#Snowball)

[Snowmobile](#Snowmobile)

# Snowball

Q: What happened with the original 50 TB and 80 TB AWS Snowball devices?

The original Snowball devices were transitioned out of service and Snowball Edge Storage Optimized are now the primary devices used for data transfer.

Q: Can I still order the original Snowball 50 TB and 80 TB devices?

No. For data transfer needs now, please select the Snowball Edge Storage Optimized devices.

--------

Can import to S3 and export from S3.

* Must set up an S3 bucket before using Snowball.

A petabyte-scale data transport solution.

It's a way to get a huge amount of data into AWS. 

Deals with problems like high network costs, security issues, etc. 

Comes in either size:

* 50TB
* 80TB

Once the data transfer job has been processed, AWS performs a *software erasure* of the Snowball appliance.

### When Snowball arrives...

When it arrives, it is a large, heavy, plastic box. You open the front door and the back door. In the back are the power cables and ethernet cables. Plug it in.

Turn it on - it's noisy! There is a screen on the front that will you when it's ready. 

You must go into the snowball dashboard to get your credentials to authenticate your access to the apliance. 

To start it:

```
./snowball start -i network-location -m manifest-file-name.bin -u unlock-code
```

To use it, you just copy your files over to the `s3://your-address-from-snowball`.

Amazon then picks it up, and they transfer your stuff over to an S3 bucket.


# Snowball Edge

AWS Snowball Edge is a type of Snowball device with on-board storage and compute power for select AWS capabilities. Snowball Edge can undertake local processing and edge-computing workloads in addition to transferring data between your local environment and the AWS Cloud.

Each Snowball Edge device can transport data at speeds faster than the internet. This transport is done by shipping the data in the appliances through a regional carrier. The appliances are rugged shipping containers, complete with E Ink shipping labels. The AWS Snowball Edge device differs from the standard Snowball because it can bring the power of the AWS Cloud to your on-premises location, with local storage and compute functionality.

Snowball Edge devices have three options for device configurations – storage optimized, compute optimized, and with GPU.



There is also a Snowball Edge, which is *100TB* of storage and computer abilities. 

This thing gives not just *storage*, but also *compute*! 

It can help support local workloads in remote or offline locations. You can stick one of these things in an aircraft as you're testing the airplane. It's like having a mini AWS available. 

# Snowmobile

Migration & Transfer > Snowball

Snowmobile is an Exabyte-scale data transfer service. 

* You can transfer up to *100 petabyes (PB)* (1,000 terabytes) per Snomobile. The thing is pulled by a Semi truck. 

Can move video libraries, and other huge amounts of data.