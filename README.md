# The competition

Want a free ZuriHac T-shirt even though they're not free anymore?

See if you can connect to our server below and work with others on the ZuriHac Discord in the #contracts4tshirts channel to bundle up to 20 `PendingTShirtOrder`s together and claim the prize. Starting your own `TShirtOrder` is easy, just follow the steps below. But coding and cooperation increase your chances of winning!

Have questions/hit snags? Find us in the channel during ZuriHac, or join our forum at https://discuss.daml.com and ask for anything except the complete answer :)

# Prequisites

## Canton

We need a distributed DAML interpreter. We will use the new (alpha version)
DAML ledger integration named "Canton" for this purpose.

Download the latest release from
https://github.com/digital-asset/canton/releases/tag/v0.15.1

```
tar -xvzf canton-0.15.1.tar.gz
```

## DAML SDK

While any recent DAML SDK version should work, we'll use the same version as
Canton 0.15.1 is based on:
DAML SDK: 1.2.0-snapshot.20200527.4268.0.acc5a21c

Follow: https://docs.daml.com/getting-started/installation.html

# Building your project
```
git clone this_repo

cd contracts-for-tshirts/src/freeshirts
daml build
```

# Global (Live) or Local (Testing)?

## To connect to the Global Canton (Live) to get your T-shirt

Start Canton with a simple topology that starts two participants *with persistence*

```
cd contracts-for-tshirts
../canton-0.15.1/bin/canton -v \
  -c persistence.conf,participants.conf \
  --bootstrap=connect-global.canton
```
Next you'll want to make sure your participant node is connected to the Global Canton.
From the Canton CLI run:
```
# ping the zurihac participant node
val shop = ParticipantId(UniqueIdentifier.tryFromString("freeshirts::01416588161bd9e1ecec1295a6358576b109f6e3cd985e22e92014d02e5757d48c"))
participant1.health.ping(shop)
```
Now, from the Canton CLI again, upload the DAR and provision your parties to your local participant so it knows how to talk to the Global Canton.

```
participants.local.dars.upload("/path/to/contracts-for-tshirts/src/freeshirts/freeshirts.dar")
val submitter = participant1.parties.enable("ChooseYourPartyName")
println((shop.toLf, submitter.toLf))
```

## To setup a Local Domain (Testing) to test your Code

Start Canton with a simple topology that starts two participants *without persistence*

```
cd contracts-for-tshirts
../canton-0.15.1/bin/canton -v \
  -c participants.conf \
  --bootstrap=connect-global.canton
```

Now for locally testing (without talking to the global domain) you need to define your own `shop` on `participant1` and have your `submitter` on `participant2`. And just like with the remote connection we need to upload the DAR to our local node first.

From the Canton CLI run:
```
participants.local.dars.upload("/path/to/contracts-for-tshirts/src/freeshirts/freeshirts.dar")
val submitter = participant1.parties.enable("ChooseYourPartyName")
val shop = participant2.parties.enable("shop")
println((shop.toLf, submitter.toLf))
```

# Configuring submit.json and query.json

Regardless of how you setup your nodes (Global Canton or Local) you'll now interact with them in the same way using daml script. To do this you need to copy/paste the `shop` and `submitter` UID strings into `shirts/submit.json` and `shirts/query.json`. Make sure to also modify `submit.json` for your order info.


If using the global domain you will send all submissions and queries to it through your local Participant 1 node (this connection happens with the `connect-global.canton` file).

If doing local testing you'll use Participant 1 for doing submissions and Participant 2 for querying the shop.

Your nodes will be running on the following PORTs:

Participant 1: 5011

Participant 2: 5021

To submit an order for a shirt (ie. your participant node) do:
```
cd contracts-for-tshirts/src/participation
daml build -o participation.dar
daml script --dar participation.dar --script-name Setup:submitChoice --ledger-host localhost --ledger-port PORT --input-file submit.json
```
To query current orders do:
```
cd contracts-for-tshirts/shirts
daml script --dar participation.dar --script-name Setup:queryExisting --ledger-host localhost --ledger-port PORT --input-file query.json --output-file output.txt
```

# The official rules

If you give out a limited quantity of free shirts, you need some legal prose
in case there are some overly eager participants. As ZuriHac and Digital Asset Switerzland GmbH
are Swiss companies, we'll do this here in German.

If you can't understand German, please use some online translation service.
In short: You can't sue us to get a shirt and we won't use your data in any other way than dispatching
a shirt to you (if you win).

```
Die Teilnahme am Wettbewerb ist kostenlos. Es besteht keine Kaufverpflichtung. 
Teilnahmeberechtigt sind natürliche Personen. Die Teilnahme setzt voraus, dass der Teilnehmer 
seine Teilname über https://zurihac.canton.global als TShirtOrder Contract anmeldet. 
Der Teilnehmer akzeptiert die für Digital Asset GmbH existierende 
Datenschutzerklärung: https://www.digitalasset.com/privacy-policy-1 
Die erhobenen Teilnahmedaten werden ausschliesslich zur Versendung der T-Shirts verwendet.  
Die Daten werden von der Digital Asset Switzerland GmbH an den offiziellen ZuriHac  
Shop https://shop.zfoh.ch/ zum Versand der T-Shirts weitergeleitet und danach vernichtet. 
Mitarbeiter der Firma DigitalAsset (weltweit) sind von der Teilnahme ausgeschlossen. 
Der Rechtsweg ist ausgeschlossen. Der Gewinn kann nicht in bar abgeloest werden. Pro  
Teilnehmer ist nur ein Gewinn pro Gewinnspiel möglich. Manipulationen von Gewinnspielen  
(auch der blosse Versuch) wird zur Anzeige gebracht. Die Digital Asset Switerzland GmbH  
ist berechtigt, Änderungen am Gewinn vorzunehmen. Der Rechtsweg ist ausgeschlossen. 
Die Gewinner werden von Digital Asset Schweiz im Sinne des Wettbewerbes bestimmt.  
(Die früheste und am besten koordinierte zeitgleiche Einreichung von Teilnahmen.)  
Die Gewinner werden mittels Kontrakt auf dem Ledger über ihren Gewinn benachrichtigt. 
In bestimmten Fällen kann die Gewinnbenachrichtigung auch direkt über die Zustellung des Gewinns erfolgen.  
Die durch den Teilnehmer bereitgestellten Daten werden für den Zeitraum der Durchführung des Gewinnspiels 
mittels einer Datenbank verarbeitet. Über das Gewinnspiel wird kein Schriftverkehr geführt.
```
