# Check AWS Route53 Domain Name Availability

- Python 3.7.2 
- aws-cli 1.16.96 
- botocore 1.12.86:
```python
import re
import subprocess
import json

name_check = "qi"  # => Domain Name to Check
aws = "aws route53domains check-domain-availability --region us-east-1 --domain-name {}"

# raw html text scratched from AWS Route 53 Web Page
text1 = '<optgroup label="Popular"><option value=".com">.com - $12.00</option><option value=".net">.net - $11.00</option><option value=".org">.org - $12.00</option><option value=".com.au">.com.au - $15.00</option><option value=".co.uk">.co.uk - $9.00</option><option value=".io">.io - $39.00</option><option value=".info">.info - $12.00</option><option value=".co">.co - $25.00</option><option value=".de">.de - $9.00</option><option value=".ca">.ca - $13.00</option><option value=".me">.me - $17.00</option><option value=".us">.us - $15.00</option><option value=".eu">.eu - $13.00</option></optgroup>'
text2 = '<optgroup label="Other"><option value=".ac">.ac - $48.00</option><option value=".academy">.academy - $32.00</option><option value=".accountants">.accountants - $94.00</option><option value=".adult">.adult - $100.00</option><option value=".agency">.agency - $19.00</option><option value=".apartments">.apartments - $47.00</option><option value=".associates">.associates - $29.00</option><option value=".auction">.auction - $29.00</option><option value=".band">.band - $22.00</option><option value=".bargains">.bargains - $30.00</option><option value=".be">.be - $9.00</option><option value=".berlin">.berlin - $52.00</option><option value=".bike">.bike - $32.00</option><option value=".bingo">.bingo - $47.00</option><option value=".biz">.biz - $16.00</option><option value=".black">.black - $43.00</option><option value=".blue">.blue - $15.00</option><option value=".boutique">.boutique - $30.00</option><option value=".builders">.builders - $32.00</option><option value=".business">.business - $18.00</option><option value=".buzz">.buzz - $37.00</option><option value=".cab">.cab - $32.00</option><option value=".cafe">.cafe - $31.00</option><option value=".camera">.camera - $46.00</option><option value=".camp">.camp - $46.00</option><option value=".capital">.capital - $47.00</option><option value=".cards">.cards - $29.00</option><option value=".care">.care - $29.00</option><option value=".careers">.careers - $35.00</option><option value=".cash">.cash - $29.00</option><option value=".casino">.casino - $141.00</option><option value=".catering">.catering - $29.00</option><option value=".cc">.cc - $12.00</option><option value=".center">.center - $21.00</option><option value=".ceo">.ceo - $74.00</option><option value=".ch">.ch - $12.00</option><option value=".chat">.chat - $29.00</option><option value=".cheap">.cheap - $30.00</option><option value=".church">.church - $29.00</option><option value=".city">.city - $19.00</option><option value=".claims">.claims - $47.00</option><option value=".cleaning">.cleaning - $46.00</option><option value=".click">.click - $10.00</option><option value=".clinic">.clinic - $47.00</option><option value=".clothing">.clothing - $32.00</option><option value=".cloud">.cloud - $23.00</option><option value=".club">.club - $12.00</option><option value=".co.nz">.co.nz - $24.00</option><option value=".co.za">.co.za - $13.00</option><option value=".coach">.coach - $47.00</option><option value=".codes">.codes - $35.00</option><option value=".coffee">.coffee - $32.00</option><option value=".college">.college - $69.00</option><option value=".com.mx">.com.mx - $34.00</option><option value=".community">.community - $29.00</option><option value=".company">.company - $18.00</option><option value=".computer">.computer - $32.00</option><option value=".condos">.condos - $49.00</option><option value=".construction">.construction - $32.00</option><option value=".consulting">.consulting - $29.00</option><option value=".contractors">.contractors - $32.00</option><option value=".cool">.cool - $30.00</option><option value=".coupons">.coupons - $51.00</option><option value=".credit">.credit - $94.00</option><option value=".creditcard">.creditcard - $141.00</option><option value=".cruises">.cruises - $49.00</option><option value=".dance">.dance - $22.00</option><option value=".dating">.dating - $49.00</option><option value=".deals">.deals - $29.00</option><option value=".delivery">.delivery - $47.00</option><option value=".democrat">.democrat - $30.00</option><option value=".dental">.dental - $47.00</option><option value=".diamonds">.diamonds - $35.00</option><option value=".digital">.digital - $29.00</option><option value=".direct">.direct - $29.00</option><option value=".directory">.directory - $21.00</option><option value=".discount">.discount - $29.00</option><option value=".dog">.dog - $46.00</option><option value=".domains">.domains - $32.00</option><option value=".education">.education - $21.00</option><option value=".email">.email - $25.00</option><option value=".energy">.energy - $94.00</option><option value=".engineering">.engineering - $47.00</option><option value=".enterprises">.enterprises - $32.00</option><option value=".equipment">.equipment - $21.00</option><option value=".es">.es - $10.00</option><option value=".estate">.estate - $32.00</option><option value=".events">.events - $30.00</option><option value=".exchange">.exchange - $29.00</option><option value=".expert">.expert - $49.00</option><option value=".exposed">.exposed - $19.00</option><option value=".express">.express - $31.00</option><option value=".fail">.fail - $29.00</option><option value=".farm">.farm - $32.00</option><option value=".fi">.fi - $24.00</option><option value=".finance">.finance - $47.00</option><option value=".financial">.financial - $47.00</option><option value=".fish">.fish - $29.00</option><option value=".fitness">.fitness - $29.00</option><option value=".flights">.flights - $49.00</option><option value=".florist">.florist - $32.00</option><option value=".fm">.fm - $92.00</option><option value=".football">.football - $19.00</option><option value=".forsale">.forsale - $29.00</option><option value=".foundation">.foundation - $30.00</option><option value=".fr">.fr - $12.00</option><option value=".fund">.fund - $47.00</option><option value=".furniture">.furniture - $47.00</option><option value=".futbol">.futbol - $12.00</option><option value=".fyi">.fyi - $20.00</option><option value=".gallery">.gallery - $21.00</option><option value=".gg">.gg - $75.00</option><option value=".gift">.gift - $20.00</option><option value=".gifts">.gifts - $19.00</option><option value=".glass">.glass - $46.00</option><option value=".global">.global - $71.00</option><option value=".gold">.gold - $101.00</option><option value=".golf">.golf - $51.00</option><option value=".graphics">.graphics - $21.00</option><option value=".gratis">.gratis - $19.00</option><option value=".green">.green - $71.00</option><option value=".gripe">.gripe - $29.00</option><option value=".guide">.guide - $29.00</option><option value=".guru">.guru - $25.00</option><option value=".haus">.haus - $29.00</option><option value=".healthcare">.healthcare - $47.00</option><option value=".help">.help - $30.00</option><option value=".hiv">.hiv - $254.00</option><option value=".hockey">.hockey - $51.00</option><option value=".holdings">.holdings - $35.00</option><option value=".holiday">.holiday - $35.00</option><option value=".host">.host - $93.00</option><option value=".house">.house - $32.00</option><option value=".im">.im - $19.00</option><option value=".immo">.immo - $29.00</option><option value=".immobilien">.immobilien - $30.00</option><option value=".in">.in - $15.00</option><option value=".industries">.industries - $29.00</option><option value=".ink">.ink - $29.00</option><option value=".institute">.institute - $21.00</option><option value=".insure">.insure - $47.00</option><option value=".international">.international - $21.00</option><option value=".investments">.investments - $94.00</option><option value=".irish">.irish - $36.00</option><option value=".it">.it - $15.00</option><option value=".jewelry">.jewelry - $51.00</option><option value=".jp">.jp - $90.00</option><option value=".kaufen">.kaufen - $30.00</option><option value=".kim">.kim - $15.00</option><option value=".kitchen">.kitchen - $46.00</option><option value=".kiwi">.kiwi - $32.00</option><option value=".land">.land - $32.00</option><option value=".lease">.lease - $47.00</option><option value=".legal">.legal - $47.00</option><option value=".lgbt">.lgbt - $43.00</option><option value=".life">.life - $29.00</option><option value=".lighting">.lighting - $21.00</option><option value=".limited">.limited - $29.00</option><option value=".limo">.limo - $35.00</option><option value=".link">.link - $10.00</option><option value=".live">.live - $23.00</option><option value=".loan">.loan - $31.00</option><option value=".loans">.loans - $94.00</option><option value=".lol">.lol - $31.00</option><option value=".maison">.maison - $49.00</option><option value=".management">.management - $21.00</option><option value=".marketing">.marketing - $32.00</option><option value=".mba">.mba - $31.00</option><option value=".me.uk">.me.uk - $8.00</option><option value=".media">.media - $29.00</option><option value=".memorial">.memorial - $47.00</option><option value=".mobi">.mobi - $12.00</option><option value=".moda">.moda - $22.00</option><option value=".money">.money - $29.00</option><option value=".mortgage">.mortgage - $43.00</option><option value=".movie">.movie - $306.00</option><option value=".mx">.mx - $34.00</option><option value=".name">.name - $9.00</option><option value=".net.au">.net.au - $15.00</option><option value=".net.nz">.net.nz - $24.00</option><option value=".network">.network - $19.00</option><option value=".news">.news - $23.00</option><option value=".ninja">.ninja - $18.00</option><option value=".nl">.nl - $10.00</option><option value=".onl">.onl - $15.00</option><option value=".online">.online - $39.00</option><option value=".org.nz">.org.nz - $24.00</option><option value=".org.uk">.org.uk - $9.00</option><option value=".partners">.partners - $49.00</option><option value=".parts">.parts - $29.00</option><option value=".photo">.photo - $30.00</option><option value=".photography">.photography - $21.00</option><option value=".photos">.photos - $21.00</option><option value=".pics">.pics - $30.00</option><option value=".pictures">.pictures - $10.00</option><option value=".pink">.pink - $15.00</option><option value=".pizza">.pizza - $47.00</option><option value=".place">.place - $29.00</option><option value=".plumbing">.plumbing - $46.00</option><option value=".plus">.plus - $31.00</option><option value=".poker">.poker - $43.00</option><option value=".porn">.porn - $100.00</option><option value=".pro">.pro - $14.00</option><option value=".productions">.productions - $30.00</option><option value=".properties">.properties - $30.00</option><option value=".pub">.pub - $22.00</option><option value=".qpon">.qpon - $15.00</option><option value=".recipes">.recipes - $35.00</option><option value=".red">.red - $15.00</option><option value=".reise">.reise - $101.00</option><option value=".reisen">.reisen - $19.00</option><option value=".rentals">.rentals - $30.00</option><option value=".repair">.repair - $32.00</option><option value=".report">.report - $19.00</option><option value=".republican">.republican - $29.00</option><option value=".restaurant">.restaurant - $47.00</option><option value=".reviews">.reviews - $22.00</option><option value=".rip">.rip - $17.00</option><option value=".rocks">.rocks - $12.00</option><option value=".ruhr">.ruhr - $30.00</option><option value=".run">.run - $20.00</option><option value=".sale">.sale - $29.00</option><option value=".sarl">.sarl - $29.00</option><option value=".school">.school - $29.00</option><option value=".schule">.schule - $19.00</option><option value=".se">.se - $23.00</option><option value=".services">.services - $29.00</option><option value=".sex">.sex - $100.00</option><option value=".sexy">.sexy - $65.00</option><option value=".sh">.sh - $48.00</option><option value=".shiksha">.shiksha - $16.00</option><option value=".shoes">.shoes - $46.00</option><option value=".show">.show - $31.00</option><option value=".singles">.singles - $30.00</option><option value=".soccer">.soccer - $20.00</option><option value=".social">.social - $32.00</option><option value=".solar">.solar - $46.00</option><option value=".solutions">.solutions - $25.00</option><option value=".studio">.studio - $23.00</option><option value=".style">.style - $29.00</option><option value=".sucks">.sucks - $282.00</option><option value=".supplies">.supplies - $19.00</option><option value=".supply">.supply - $19.00</option><option value=".support">.support - $21.00</option><option value=".surgery">.surgery - $47.00</option><option value=".systems">.systems - $21.00</option><option value=".tattoo">.tattoo - $45.00</option><option value=".tax">.tax - $47.00</option><option value=".taxi">.taxi - $51.00</option><option value=".team">.team - $31.00</option><option value=".technology">.technology - $21.00</option><option value=".tennis">.tennis - $47.00</option><option value=".theater">.theater - $51.00</option><option value=".tienda">.tienda - $50.00</option><option value=".tips">.tips - $21.00</option><option value=".tires">.tires - $94.00</option><option value=".today">.today - $21.00</option><option value=".tools">.tools - $29.00</option><option value=".tours">.tours - $51.00</option><option value=".town">.town - $29.00</option><option value=".toys">.toys - $46.00</option><option value=".trade">.trade - $29.00</option><option value=".training">.training - $27.00</option><option value=".tv">.tv - $32.00</option><option value=".uk">.uk - $9.00</option><option value=".university">.university - $47.00</option><option value=".uno">.uno - $30.00</option><option value=".vacations">.vacations - $35.00</option><option value=".vc">.vc - $33.00</option><option value=".vegas">.vegas - $57.00</option><option value=".ventures">.ventures - $35.00</option><option value=".vg">.vg - $35.00</option><option value=".viajes">.viajes - $49.00</option><option value=".video">.video - $22.00</option><option value=".villas">.villas - $35.00</option><option value=".vision">.vision - $29.00</option><option value=".voyage">.voyage - $50.00</option><option value=".watch">.watch - $37.00</option><option value=".website">.website - $23.00</option><option value=".wien">.wien - $29.00</option><option value=".wiki">.wiki - $30.00</option><option value=".works">.works - $30.00</option><option value=".world">.world - $29.00</option><option value=".wtf">.wtf - $29.00</option><option value=".xyz">.xyz - $12.00</option><option value=".zone">.zone - $32.00</option></optgroup>'


def main():
    domain_name_list = get_domain_name_list(name_check, text1 + text2)
    for domain_name in domain_name_list:
        print(find(domain_name))


def find(domain_name):
    cmd = aws.format(domain_name)
    result = subprocess.run(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE, universal_newlines=True, shell=True)
    try:
        status = json.loads(result.stdout)['Availability']
    except Exception:
        status = 'Error'
    return f"{domain_name}: {status}"


def get_domain_name_list(name, text):
    pattern = re.compile('value="(.+?)">')
    return [name + domain for domain in sorted(pattern.findall(text))]


if __name__ == "__main__":
    main()
```

# 2019-04-27 Extracted AWS Route53 Supported Domains:
```
.ac
.academy
.accountants
.adult
.agency
.apartments
.associates
.auction
.band
.bargains
.be
.berlin
.bike
.bingo
.biz
.black
.blue
.boutique
.builders
.business
.buzz
.ca
.cab
.cafe
.camera
.camp
.capital
.cards
.care
.careers
.cash
.casino
.catering
.cc
.center
.ceo
.ch
.chat
.cheap
.church
.city
.claims
.cleaning
.click
.clinic
.clothing
.cloud
.club
.co
.co.nz
.co.uk
.co.za
.coach
.codes
.coffee
.college
.com
.com.au
.com.mx
.community
.company
.computer
.condos
.construction
.consulting
.contractors
.cool
.coupons
.credit
.creditcard
.cruises
.dance
.dating
.de
.deals
.delivery
.democrat
.dental
.diamonds
.digital
.direct
.directory
.discount
.dog
.domains
.education
.email
.energy
.engineering
.enterprises
.equipment
.es
.estate
.eu
.events
.exchange
.expert
.exposed
.express
.fail
.farm
.fi
.finance
.financial
.fish
.fitness
.flights
.florist
.fm
.football
.forsale
.foundation
.fr
.fund
.furniture
.futbol
.fyi
.gallery
.gg
.gift
.gifts
.glass
.global
.gold
.golf
.graphics
.gratis
.green
.gripe
.guide
.guru
.haus
.healthcare
.help
.hiv
.hockey
.holdings
.holiday
.host
.house
.im
.immo
.immobilien
.in
.industries
.info
.ink
.institute
.insure
.international
.investments
.io
.irish
.it
.jewelry
.jp
.kaufen
.kim
.kitchen
.kiwi
.land
.lease
.legal
.lgbt
.life
.lighting
.limited
.limo
.link
.live
.loan
.loans
.lol
.maison
.management
.marketing
.mba
.me
.me.uk
.media
.memorial
.mobi
.moda
.money
.mortgage
.movie
.mx
.name
.net
.net.au
.net.nz
.network
.news
.ninja
.nl
.onl
.online
.org
.org.nz
.org.uk
.partners
.parts
.photo
.photography
.photos
.pics
.pictures
.pink
.pizza
.place
.plumbing
.plus
.poker
.porn
.pro
.productions
.properties
.pub
.qpon
.recipes
.red
.reise
.reisen
.rentals
.repair
.report
.republican
.restaurant
.reviews
.rip
.rocks
.ruhr
.run
.sale
.sarl
.school
.schule
.se
.services
.sex
.sexy
.sh
.shiksha
.shoes
.show
.singles
.soccer
.social
.solar
.solutions
.studio
.style
.sucks
.supplies
.supply
.support
.surgery
.systems
.tattoo
.tax
.taxi
.team
.technology
.tennis
.theater
.tienda
.tips
.tires
.today
.tools
.tours
.town
.toys
.trade
.training
.tv
.uk
.university
.uno
.us
.vacations
.vc
.vegas
.ventures
.vg
.viajes
.video
.villas
.vision
.voyage
.watch
.website
.wien
.wiki
.works
.world
.wtf
.xyz
.zone
```

# 2019-04-27 Results
```
qi.ac: UNAVAILABLE
qi.academy: UNAVAILABLE
qi.accountants: AVAILABLE
qi.adult: UNAVAILABLE
qi.agency: UNAVAILABLE
qi.apartments: UNAVAILABLE
qi.associates: UNAVAILABLE
qi.auction: UNAVAILABLE
qi.band: UNAVAILABLE
qi.bargains: UNAVAILABLE
qi.be: UNAVAILABLE
qi.berlin: UNAVAILABLE
qi.bike: UNAVAILABLE
qi.bingo: AVAILABLE
qi.biz: UNAVAILABLE
qi.black: PENDING
qi.blue: PENDING
qi.boutique: UNAVAILABLE
qi.builders: UNAVAILABLE
qi.business: UNAVAILABLE
qi.buzz: PENDING
qi.ca: UNAVAILABLE
qi.cab: UNAVAILABLE
qi.cafe: UNAVAILABLE
qi.camera: UNAVAILABLE
qi.camp: UNAVAILABLE
qi.capital: UNAVAILABLE
qi.cards: UNAVAILABLE
qi.care: UNAVAILABLE
qi.careers: UNAVAILABLE
qi.cash: UNAVAILABLE
qi.casino: Error
qi.catering: PENDING
qi.cc: INVALID_NAME_FOR_TLD
qi.center: PENDING
qi.ceo: UNAVAILABLE
qi.ch: INVALID_NAME_FOR_TLD
qi.chat: UNAVAILABLE
qi.cheap: UNAVAILABLE
qi.church: UNAVAILABLE
qi.city: UNAVAILABLE
qi.claims: UNAVAILABLE
qi.cleaning: UNAVAILABLE
qi.click: UNAVAILABLE
qi.clinic: UNAVAILABLE
qi.clothing: UNAVAILABLE
qi.cloud: PENDING
qi.club: PENDING
qi.co: INVALID_NAME_FOR_TLD
qi.co.nz: PENDING
qi.co.uk: UNAVAILABLE
qi.co.za: UNAVAILABLE
qi.coach: UNAVAILABLE
qi.codes: UNAVAILABLE
qi.coffee: Error
qi.college: INVALID_NAME_FOR_TLD
qi.com: UNAVAILABLE
qi.com.au: PENDING
qi.com.mx: INVALID_NAME_FOR_TLD
qi.community: UNAVAILABLE
qi.company: Error
qi.computer: UNAVAILABLE
qi.condos: UNAVAILABLE
qi.construction: UNAVAILABLE
qi.consulting: UNAVAILABLE
qi.contractors: UNAVAILABLE
qi.cool: UNAVAILABLE
qi.coupons: UNAVAILABLE
qi.credit: Error
qi.creditcard: AVAILABLE
qi.cruises: UNAVAILABLE
qi.dance: UNAVAILABLE
qi.dating: UNAVAILABLE
qi.de: UNAVAILABLE
qi.deals: UNAVAILABLE
qi.delivery: UNAVAILABLE
qi.democrat: UNAVAILABLE
qi.dental: PENDING
qi.diamonds: UNAVAILABLE
qi.digital: UNAVAILABLE
qi.direct: UNAVAILABLE
qi.directory: UNAVAILABLE
qi.discount: UNAVAILABLE
qi.dog: Error
qi.domains: UNAVAILABLE
qi.education: Error
qi.email: UNAVAILABLE
qi.energy: Error
qi.engineering: UNAVAILABLE
qi.enterprises: UNAVAILABLE
qi.equipment: UNAVAILABLE
qi.es: INVALID_NAME_FOR_TLD
qi.estate: PENDING
qi.eu: PENDING
qi.events: UNAVAILABLE
qi.exchange: UNAVAILABLE
qi.expert: PENDING
qi.exposed: PENDING
qi.express: UNAVAILABLE
qi.fail: UNAVAILABLE
qi.farm: UNAVAILABLE
qi.fi: PENDING
qi.finance: UNAVAILABLE
qi.financial: Error
qi.fish: PENDING
qi.fitness: UNAVAILABLE
qi.flights: UNAVAILABLE
qi.florist: PENDING
qi.fm: UNAVAILABLE
qi.football: AVAILABLE
qi.forsale: UNAVAILABLE
qi.foundation: UNAVAILABLE
qi.fr: UNAVAILABLE
qi.fund: Error
qi.furniture: UNAVAILABLE
qi.futbol: Error
qi.fyi: UNAVAILABLE
qi.gallery: UNAVAILABLE
qi.gg: UNAVAILABLE
qi.gift: UNAVAILABLE
qi.gifts: UNAVAILABLE
qi.glass: UNAVAILABLE
qi.global: Error
qi.gold: UNAVAILABLE
qi.golf: UNAVAILABLE
qi.graphics: UNAVAILABLE
qi.gratis: UNAVAILABLE
qi.green: PENDING
qi.gripe: PENDING
qi.guide: Error
qi.guru: UNAVAILABLE
qi.haus: UNAVAILABLE
qi.healthcare: UNAVAILABLE
qi.help: UNAVAILABLE
qi.hiv: AVAILABLE
qi.hockey: PENDING
qi.holdings: UNAVAILABLE
qi.holiday: UNAVAILABLE
qi.host: UNAVAILABLE
qi.house: UNAVAILABLE
qi.im: PENDING
qi.immo: UNAVAILABLE
qi.immobilien: UNAVAILABLE
qi.in: INVALID_NAME_FOR_TLD
qi.industries: UNAVAILABLE
qi.info: INVALID_NAME_FOR_TLD
qi.ink: INVALID_NAME_FOR_TLD
qi.institute: UNAVAILABLE
qi.insure: UNAVAILABLE
qi.international: UNAVAILABLE
qi.investments: UNAVAILABLE
qi.io: UNAVAILABLE
qi.irish: UNAVAILABLE
qi.it: DONT_KNOW
qi.jewelry: UNAVAILABLE
qi.jp: INVALID_NAME_FOR_TLD
qi.kaufen: UNAVAILABLE
qi.kim: PENDING
qi.kitchen: UNAVAILABLE
qi.kiwi: INVALID_NAME_FOR_TLD
qi.land: UNAVAILABLE
qi.lease: UNAVAILABLE
qi.legal: AVAILABLE
qi.lgbt: UNAVAILABLE
qi.life: Error
qi.lighting: UNAVAILABLE
qi.limited: UNAVAILABLE
qi.limo: Error
qi.link: UNAVAILABLE
qi.live: UNAVAILABLE
qi.loan: UNAVAILABLE
qi.loans: UNAVAILABLE
qi.lol: PENDING
qi.maison: PENDING
qi.management: UNAVAILABLE
qi.marketing: UNAVAILABLE
qi.mba: UNAVAILABLE
qi.me: INVALID_NAME_FOR_TLD
qi.me.uk: UNAVAILABLE
qi.media: UNAVAILABLE
qi.memorial: AVAILABLE
qi.mobi: INVALID_NAME_FOR_TLD
qi.moda: UNAVAILABLE
qi.money: UNAVAILABLE
qi.mortgage: UNAVAILABLE
qi.movie: AVAILABLE
qi.mx: INVALID_NAME_FOR_TLD
qi.name: INVALID_NAME_FOR_TLD
qi.net: UNAVAILABLE
qi.net.au: PENDING
qi.net.nz: PENDING
qi.network: UNAVAILABLE
qi.news: UNAVAILABLE
qi.ninja: UNAVAILABLE
qi.nl: INVALID_NAME_FOR_TLD
qi.onl: Error
qi.online: Error
qi.org: INVALID_NAME_FOR_TLD
qi.org.nz: Error
qi.org.uk: Error
qi.partners: UNAVAILABLE
qi.parts: Error
qi.photo: UNAVAILABLE
qi.photography: UNAVAILABLE
qi.photos: UNAVAILABLE
qi.pics: PENDING
qi.pictures: UNAVAILABLE
qi.pink: PENDING
qi.pizza: UNAVAILABLE
qi.place: INVALID_NAME_FOR_TLD
qi.plumbing: UNAVAILABLE
qi.plus: UNAVAILABLE
qi.poker: UNAVAILABLE
qi.porn: Error
qi.pro: Error
qi.productions: UNAVAILABLE
qi.properties: UNAVAILABLE
qi.pub: Error
qi.qpon: UNAVAILABLE
qi.recipes: UNAVAILABLE
qi.red: PENDING
qi.reise: UNAVAILABLE
qi.reisen: UNAVAILABLE
qi.rentals: UNAVAILABLE
qi.repair: UNAVAILABLE
qi.report: UNAVAILABLE
qi.republican: UNAVAILABLE
qi.restaurant: UNAVAILABLE
qi.reviews: UNAVAILABLE
qi.rip: UNAVAILABLE
qi.rocks: UNAVAILABLE
qi.ruhr: INVALID_NAME_FOR_TLD
qi.run: Error
qi.sale: UNAVAILABLE
qi.sarl: Error
qi.school: UNAVAILABLE
qi.schule: UNAVAILABLE
qi.se: UNAVAILABLE
qi.services: UNAVAILABLE
qi.sex: UNAVAILABLE
qi.sexy: PENDING
qi.sh: UNAVAILABLE
qi.shiksha: PENDING
qi.shoes: UNAVAILABLE
qi.show: UNAVAILABLE
qi.singles: Error
qi.soccer: UNAVAILABLE
qi.social: Error
qi.solar: Error
qi.solutions: UNAVAILABLE
qi.studio: UNAVAILABLE
qi.style: UNAVAILABLE
qi.sucks: INVALID_NAME_FOR_TLD
qi.supplies: UNAVAILABLE
qi.supply: PENDING
qi.support: UNAVAILABLE
qi.surgery: UNAVAILABLE
qi.systems: UNAVAILABLE
qi.tattoo: UNAVAILABLE
qi.tax: UNAVAILABLE
qi.taxi: UNAVAILABLE
qi.team: UNAVAILABLE
qi.technology: UNAVAILABLE
qi.tennis: AVAILABLE
qi.theater: UNAVAILABLE
qi.tienda: AVAILABLE
qi.tips: UNAVAILABLE
qi.tires: AVAILABLE
qi.today: UNAVAILABLE
qi.tools: UNAVAILABLE
qi.tours: UNAVAILABLE
qi.town: UNAVAILABLE
qi.toys: UNAVAILABLE
qi.trade: UNAVAILABLE
qi.training: UNAVAILABLE
qi.tv: INVALID_NAME_FOR_TLD
qi.uk: UNAVAILABLE
qi.university: UNAVAILABLE
qi.uno: PENDING
qi.us: INVALID_NAME_FOR_TLD
qi.vacations: PENDING
qi.vc: UNAVAILABLE
qi.vegas: PENDING
qi.ventures: UNAVAILABLE
qi.vg: PENDING
qi.viajes: UNAVAILABLE
qi.video: UNAVAILABLE
qi.villas: UNAVAILABLE
qi.vision: UNAVAILABLE
qi.voyage: UNAVAILABLE
qi.watch: PENDING
qi.website: UNAVAILABLE
qi.wien: PENDING
qi.wiki: INVALID_NAME_FOR_TLD
qi.works: UNAVAILABLE
qi.world: UNAVAILABLE
qi.wtf: UNAVAILABLE
qi.xyz: UNAVAILABLE
qi.zone: UNAVAILABLE
```
