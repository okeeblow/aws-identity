aws-identity
============

This script makes it easier to switch among multiple Amazon Web Services identities for the AWS command line tools, as I must at work.

Create a directory for each identity under $AWS_DIR (~/aws-identities by default). Each identity directory may contain an EC2 certificate and private key pair, an AWS credential file, or both. The certificate and private key may retain their original Amazon-generated filenames. The credential file should look like:

	AWSAccessKeyId=accesskeyhere
	AWSSecretKey=isureamtellingyoumysecretkeyrightnow

This script can generate either Bourne-like (sh, zsh, bash, ksh) or C Shell-like (csh, tcsh) syntax as chosen by the second argument 'sh' or 'csh'. Since child shell scripts can't change the environment of their parent, this output should be evaled to change your AWS environment variables. When the second argument is omitted, the script will return human-readable output describing what it changes.

I like to invoke this script like so, from my .zshrc:

	aws() {eval `bin/aws-identity $1 sh` && bin/aws-identity $1}

If a chosen identity is lacking either an EC2 keypair or an AWS credential file, those environment variables will be unset

Here's an example with an identity containing both a keypair and a credential file:

	[nreid@minamo#nreid] bin/aws-identity client1 sh
	export EC2_CERT=/Users/nreid/aws-identities/client1/cert-ARFCVP24OJED4KQP2WXYPDX7XYV62UYJ.pem && 
	export EC2_PRIVATE_KEY=/Users/nreid/aws-identities/client1/pk-ARFCVP24OJED4KQP2WXYPDX7XYV62UYJ.pem &&
	export AWS_CREDENTIAL_FILE=/Users/nreid/aws-identities/client1/aws-credentials

	[nreid@minamo#nreid] aws client1
	Switched EC2 and AWS identity to client1

	[nreid@minamo#nreid] export | grep -E 'EC2_CERT|EC2_PRIV|AWS_CRED'
	AWS_CREDENTIAL_FILE=/Users/nreid/aws-identities/client1/aws-credentials
	EC2_CERT=/Users/nreid/aws-identities/client1/cert-ARFCVP24OJED4KQP2WXYPDX7XYV62UYJ.pem
	EC2_PRIVATE_KEY=/Users/nreid/aws-identities/client1/pk-ARFCVP24OJED4KQP2WXYPDX7XYV62UYJ.pem

And another with an identity containing only an EC2 keypair:

	[nreid@minamo#nreid] bin/aws-identity client2 sh
	export EC2_CERT=/Users/nreid/aws-identities/client2/cert-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem && 
	export EC2_PRIVATE_KEY=/Users/nreid/aws-identities/client2/pk-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem &&
	unset AWS_CREDENTIAL_FILE

	[nreid@minamo#nreid] aws client2
	Switched EC2 identity to client2

	[nreid@minamo#nreid] export | grep -E 'EC2_CERT|EC2_PRIV|AWS_CRED'
	EC2_CERT=/Users/nreid/aws-identities/seaworld/cert-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem
	EC2_PRIVATE_KEY=/Users/nreid/aws-identities/seaworld/pk-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem

Lastly, CSH syntax:

	[nreid@minamo#nreid] bin/aws-identity client2 csh
	setenv EC2_CERT /Users/nreid/aws-identities/client2/cert-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem && 
	setenv EC2_PRIVATE_KEY /Users/nreid/aws-identities/client2/pk-YHGL5M3BBXFTMRYP3R42VNT32B634ESH.pem &&
	unsetenv AWS_CREDENTIAL_FILE
