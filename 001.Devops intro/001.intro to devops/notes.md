Devops→Development+Operation!

Flipkart delivers in 10 days and Amazonprime in 2 days.Your productcostis10k,whichyouwill
choose??Youwillchooseamazonbecauseitdeliversspeedly.
Similarlywehavedevops,Companiesinvest 1000 crores,soneeddeliveryofapplicationsvery
speedly,sothat'swhyweneeddevops.
SpeedlyiscalledAgility!!

LetsseeSDLC!!Everyapplicationneedsseveralstagestobedeveloped!!
2 teams→developmentandOperations
Devlopersplan,code,buildandtest

Devops1stroleisDeployment(processofinstallingapplicationinserver)
Developerwritescodeonlocaldesktop!!Togiveapplicationtoendusersweneedtoputthe
applicationtotheenduser!!Soforthatweputtheapplicationtoserver!!Andinstalling
applicationonserveriscalleddeployment!!

Afterdeploymentwedooperationslikechangeinconfigurations,updatingapplication

Thenwedomonitoring(checkinghowapplicationworkingonserver,howserverworkswith 100
users,1000users,10kusersandsoon)
Above 3 things(deployment,operationsandmonitoring)donebyoperations
Above 7 stages(plan,code,build,test,deployment,operationsandmonitoring)isSoftware
developmentlifecycle!!

Afterdevelopmentteamdevelopscodeonlocal,itsendscodetooperationsteamtoputon
server!!NowSupposecodestopsworkingonserverwhichwasworkingonlocal!

Bothfightingdev→workingonmylocal,shouldworkonlocal,sodeploymentiswrong
Ops→dontknowaboutcode,deploymentiscorrect,codeiswrong!!

Problemhereisdevteamnottoldopskiinstalljava!!

Devopsisamethodologytodeliverapplicationspeedly!!

Asafterdeployment,therearemanyversionsofsameapplication!!Andeachversionneedsto
bemaintained!!

IndevopswehavereleasestageextrathanSDLC!!

Cannotgotobackatimplementationstageasaftertestingwegetabug!!
Itissequentialdevelopmentmodel!!goingsteptostep!!

AfterwaterfallAgilemodelcomes!AgilemeansSppedly!!
Waterfall,atatimeicanworkonsinglestage!!

Develop 1 serviceandthentestthatAndthisprocessgoesontillfullapplicationdevelops!!
Assoonasyoucompletesomething,givethattotest!!
Notimgapbetweendevelopmentandtetsing

Limitations→givingtheapplicationtoclientatsametimenaa!Wearedevelopingandtesting
fastbutclientgetapplicationatsametimeasofwaterfallmodel!!

SowegettoDevopsreleasethatpartinmarketwhichisdeveloped.Hereentireapplicationis
notreleasedatatime!!herereleaseindoneinparts!!

OnITindustry,2008isverybigyearasgooglegeneratedGolang,netflixcollaboratedwithAWS
from,therecloudmarketrises!!

Devopsengineersneverwritescode!!Butsomecompaniesexpectthattoo!!

Build→maven,gradleetc
Testing→Junit,selenium
Deploy→docker

WHYDEVOPS:
Todeliverapplicationveryspeedly.

SDLC:softwaredevelopmentlifecycle.

DEPLOYMENT:Processofinstallingapplicationinserver.

=======================================================================
Application→collectionofservices!
Paytm→DTHservice,rechargeservice,scannerservice!!

SOFTWAREARCHITECTURES:
1.ONE-TIER
2.TWO-TIER
3.THREE-TIER
4.N-TIER

TIER:SERVER:LAYERallaresame!!

SERVER:serverstheservicestotheenduser.
1.webserver:
2.appserver:
3.dbserver:

WEBSERVER:
itisalsocalledasthepresentationlayer.

toshowtheapplication.
whoworks:ui/uxdevelopers
whattheyuse:webtechnologies
ex:html,css,js

APPSERVER:
Tousetheapplication.
alsocalledthelogiclayer
whowork:backenddevelopers
whattheyuse:programming
ex:java,python,c,c++,.net,go--------

WebserverAppservercommunicatebySSH(willseelater)

DBSERVER:
Italsocalledasdblayer.
Tostore&retrievedata.
whowork:dbadmins
whattheyuse:dblanguages
ex:sql,oracle,postgres,arango----

ONE-TIERARCHITECTURE:STANDALONEAPPLICATION
THEAPPWILLWORKONALOCALLAPTOP.
ALLLAYERSWILLBEONLOCALLY.
ITWILLNOTREQUIREANYINTERNETCONNECTION.
EX:VLCMEDIAPLAYER->forvlcnoneedtointernet.Nodboninternet.youlocalisvideoss
DB.Abletouseapplicationthatisapplicationlayer!!wecanuseapplicationwithoutinternetso
applicationlayeralsoonlocal!!UIalsoyoucanseewithoutinternetsoweblayeralsoonlocal!!

TWO-TIERARCHITECTURE:CLIENT-SERVERAPPLICATION
THEAPPWILLWORKONALOCALLAPTOP.
LOCAL:PRESENTATION,LOGIC DB:INTERNET
ITWILLREQUIREANYINTERNETCONNECTION.
EX:BANKINGAPPLICATIONS,JIoSavn
EX:Youtubemusicyoucanalsosee!!touseneedinternet!!
Bankingapplicationonlocalonlybuttheyneedtoconnecttoserver!!
Logicandpresentationonlocalbutdboninternetonsomeserver!!

THREE-TIER:WEBAPPLICATION(REALTIME)
APPLICATIONNONEEDTOBEONLOCAL.
EVERYTHINGWECANUSEFROMTHEINTERNET.
EX:WHATSAPP,YOUTUBE,INSTA-----
Herewearetalkingaboutlaptop!!notaboutmobileapp!!Toopenyoutubeyouneedtogoto
URLandfromtheiryoucanuseseeYTUI!!

Wehavedevelopedbankingappin2ndyearthatwashavingonetierarchitecture!!As
everythingwasonlocal!!

Ntierismicroservices!!

=======================================================================
AGENDA:CREATEAWEBSERVERANDSHOWTHELIVEENV

Serverislikeawaiterwhichservesyouwhateverserviceyouwantonrequest!!!User 1
requestedaservicecalledbiryanianduser 2 sweetsanduser3needssaladswaiter(server)
servedit.
SERVER:SERVERSERVSTHESERVICESTOENDUSER.
WEBSERVER:TOSHOWTHEAPPLICATION

TOCREATESERVERWENEEDTOHAVECLOUDACCOUNT.
AWS:EC2INSTANCES=ELASTICCOMPUTECLOUD
InAWSserveriscalledasEC2instance!!

TOCREATEEC2WENEEDTOPERFORM 7 STEPS:

SERVER=COMPUTER
1.TAGS=NAME
2.AMI(instancetype)=OS,SOFTWAREPACKAGES(alwaysuselatestversion-1version)as
latestonehavebugsorcansayn-1version
3.INSTANCE_TYPE=CPU&RAM
4.KEY_PAIR=LOGIN

Twotypeofkeyspublicandprivate,publicmanagedbyaws,privatebyuser,privateisstoredin
localbyuserin.pemfile!!
5.NETWORK=VPC(VirtualPrivateCloud),SECURITYGROUPS(portnumbers=0-65535)
YouknowWeuseVPN(virtualprivatenetwork)wherewhosoeveriwantiwillallow!!
Onmyprivatenetworkicandoanythingallowanything,disallowanything!!
LikeVPNwehaveVPC!!VPCisprivatecloudwheresecurityliesonourhand!!

Securitygroupsherewedefineportnumberwhichwewanttoexposetointernetsothatother
canuseit!!

BydefaultsecurityprotocolisSSH
SSH=SECURESHELL(22)-TOCOMMUNICATEWITHSERVERS

AllTrafficwillallowallports
Wegenerallyallowsomeportsonly!!
6.STORAGE= 8 GB- 16 TB
Defaultis8GB.
7.SUMMARY=TOREVIEW

===========================================================
Asadevopsengineeryourkey 3 principleis
understandingarchitectures, automate,writingpipeline