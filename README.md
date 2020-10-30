# Web-scraping-yahoofinance
Realizing scrape with the api yahoo finance to database SQLServer and all this with the fantastic node.js
First create your table, please read the file CreateTable.txt



```javascript
var yahooFinance = require('yahoo-finance');
var sql = require("mssql");

var fileconfig = require("./config.js");
var filelist = require("./list.js");

var config = fileconfig.config 
var list = filelist.list

function formatnumber(v){
if(v == null || v=='null' || v == undefined || v == 'undefined' || v =='Infinity'){
return 0
}else{
return v 
}
}


function formatdate(v){
if(v == null || v=='null' || v=='undefined' || v==undefined){
return '1900-01-01'
}else{return v.toLocaleString('pt-BR')  }
}

function formatname(v,v2){
if(v == null || v=='null' || v=='undefined' || v==undefined){
if(v2 == null || v2=='null' || v2=='undefined' || v2==undefined){
return v2 
}else{return v2.replace(/'/g,'')}
}else{
return v
}
}

 
var connlog = new sql.Connection(config);
connlog.connect().then(() => {
var reqlog = new sql.Request(connlog); 

const forLoop = async _ => {
console.log('Start')
 

for(x = 0;x<list.length;x++){
 

  await(function(x) {
    setTimeout(function() {
  
     yahooFinance.quote({
        symbol:list[x],
        modules: ['price', 'summaryDetail' ] // see the docs for the full list
      }, function (err,quotes) {
      if(err){}else{
      //console.log(quotes)
      return quotes
      }
      }).then((result) => { 
 
 
        if(result.price.symbol != null || result.price.symbol != undefined || result.price.symbol != 'null' || result.price.symbol != 'undefined'){
        console.log(x,result.price.shortName)
 
        reqlog.query(`
        INSERT INTO [dbo].[SUMMARY]
           ([DATA]
           ,[ATIVO]
           ,[SHORTNAME]
           ,[LONGNAME]
           ,[MAXAGE]
           ,[PRICEHINT]
           ,[PREVIOUSCLOSE]
           ,[VOPEN]
           ,[DAYLOW]
           ,[DAYHIGH]
           ,[REGULARMARKETPREVIOUSCLOSE]
           ,[REGULARMARKETOPEN]
           ,[REGULARMARKETDAYLOW]
           ,[REGULARMARKETDAYHIGH]
           ,[DIVIDENDRATE]
           ,[DIVIDENDYIELD]
           ,[EXDIVIDENDDATE]
           ,[PAYOUTRATIO]
           ,[FIVEYEARAVGDIVIDENDYIELD]
           ,[BETA]
           ,[TRAILINGPE]
           ,[FORWARDPE]
           ,[VOLUME]
           ,[REGULARMARKETVOLUME]
           ,[AVERAGEVOLUME]
           ,[AVERAGEVOLUME10DAYS]
           ,[AVERAGEDAILYVOLUME10DAY]
           ,[BID]
           ,[ASK]
           ,[BIDSIZE]
           ,[ASKSIZE]
           ,[MARKETCAP]
           ,[FIFTYTWOWEEKLOW]
           ,[FIFTYTWOWEEKHIGH]
           ,[PRICETOSALESTRAILING12MONTHS]
           ,[FIFTYDAYAVERAGE]
           ,[TWOHUNDREDDAYAVERAGE]
           ,[TRAILINGANNUALDIVIDENDRATE]
           ,[TRAILINGANNUALDIVIDENDYIELD]
           ,[CURRENCY]
           ,[FROMCURRENCY]
           ,[TOCURRENCY]
           ,[LASTMARKET]
           ,[ALGORITHM]
           ,[TRADEABLE])

            VALUES (
            getdate()
            ,'`+result.price.symbol+ `'
            ,'`+formatname(result.price.shortName,result.price.longName)+`'
            ,'`+formatname(result.price.longName,result.price.shortName)+`'
            ,'`+formatnumber(result.summaryDetail.maxAge)+ `'
            ,'`+formatnumber(result.summaryDetail.priceHint)+ `'
            ,'`+formatnumber(result.summaryDetail.previousClose)+ `'
            ,'`+formatnumber(result.summaryDetail.open)+ `'
            ,'`+formatnumber(result.summaryDetail.dayLow)+ `'
            ,'`+formatnumber(result.summaryDetail.dayHigh)+ `'
            ,'`+formatnumber(result.summaryDetail.regularMarketPreviousClose)+ `'
            ,'`+formatnumber(result.summaryDetail.regularMarketOpen)+ `'
            ,'`+formatnumber(result.summaryDetail.regularMarketDayLow)+ `'
            ,'`+formatnumber(result.summaryDetail.regularMarketDayHigh)+ `'
            ,'`+formatnumber(result.summaryDetail.dividendRate)+ `'
            ,'`+formatnumber(result.summaryDetail.dividendYield)+ `'
            ,'`+formatdate(result.summaryDetail.exDividendDate)+ `'
            ,'`+formatnumber(result.summaryDetail.payoutRatio)+ `'
            ,'`+formatnumber(result.summaryDetail.fiveYearAvgDividendYield)+ `'
            ,'`+formatnumber(result.summaryDetail.beta)+ `'
            ,'`+formatnumber(result.summaryDetail.trailingPE)+ `'
            ,'`+formatnumber(result.summaryDetail.forwardPE)+ `'
            ,'`+formatnumber(result.summaryDetail.volume)+ `'
            ,'`+formatnumber(result.summaryDetail.regularMarketVolume)+ `'
            ,'`+formatnumber(result.summaryDetail.averageVolume)+ `'
            ,'`+formatnumber(result.summaryDetail.averageVolume10days)+ `'
            ,'`+formatnumber(result.summaryDetail.averageDailyVolume10Day)+ `'
            ,'`+formatnumber(result.summaryDetail.bid)+ `'
            ,'`+formatnumber(result.summaryDetail.ask)+ `'
            ,'`+formatnumber(result.summaryDetail.bidSize)+ `'
            ,'`+formatnumber(result.summaryDetail.askSize)+ `'
            ,'`+formatnumber(result.summaryDetail.marketCap)+ `'
            ,'`+formatnumber(result.summaryDetail.fiftyTwoWeekLow)+ `'
            ,'`+formatnumber(result.summaryDetail.fiftyTwoWeekHigh)+ `'
            ,'`+formatnumber(result.summaryDetail.priceToSalesTrailing12Months)+ `'
            ,'`+formatnumber(result.summaryDetail.fiftyDayAverage)+ `'
            ,'`+formatnumber(result.summaryDetail.twoHundredDayAverage)+ `'
            ,'`+formatnumber(result.summaryDetail.trailingAnnualDividendRate)+ `'
            ,'`+formatnumber(result.summaryDetail.trailingAnnualDividendYield)+ `'
            ,'`+formatnumber(result.summaryDetail.currency)+ `'
            ,'`+formatnumber(result.summaryDetail.fromCurrency)+ `'
            ,'`+formatnumber(result.summaryDetail.toCurrency)+ `'
            ,'`+formatnumber(result.summaryDetail.lastMarket)+ `'
            ,'`+formatnumber(result.summaryDetail.algorithm)+ `'
            ,'`+formatnumber(result.summaryDetail.tradeable)+ `'
                       
            )
        `);    

      }
         
    
    }).catch((err) => {
    //console.log(`Error:${x}`)
    console.log(`Erro: ${err}`)
    })

    }, x * 1500);
  })(x);
//////////////////Close looop


}
}

forLoop()
//connlog.close();
});
```
