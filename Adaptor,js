/*
 * TripleLift adapter
 * - prebid adapter: https://github.com/tiffwu/Prebid.js/blob/master/src/adapters/triplelift.js
*/
 
var AufBid = window.AufBid || {};
 
AufBid.TripleLiftAdapter = (function() {
    var ENDPOINT = 'https://tlx.3lift.com/header/auction';
 
    return extend(AufBid.AufAdapter, {
        requestBids: function() {
            for (var formatId in this.placements) {
                if (this.placements.hasOwnProperty(formatId)) {
                    this.placements[formatId].forEach(function(placement) {
                        var query = {
                            callbackID : formatId + '_' + placement.tag + '_' + placement.size,
                            inv_code   : placement.tag,
                            callback   : this.createCallback(formatId, placement.tag, placement.size).name,
                            referrer   : location.href,
                            size       : placement.size,
                        };
 
                        // Force a response on demand disabled placements
                        if (this.debug) {
                            query.referrer = (location.hash ? location.hash + '&' : '#') + 'tripleliftTest=true';
                        }
 
                        var src = ENDPOINT + '?' + this.manager.utils.getQueryString(query);
                        this.manager.utils.loadScript(src);
                    }, this);
                }
            }
        },
 
        getBidsFromResponse: function(response, formatId, placementId) {
            if (!response || response.status === 'no_bid') {
                this.log('No bid');
                return;
            }
 
            if (response.status === 'demand_disabled') {
                this.logError('Demand disabled on placement ' + placementId);
                return;
            }
 
            this.manager.addBid({
                account: this,
                formatId: formatId,
                placementId: placementId,
                placementSize: response.width + 'x' + response.height,
                cpmRaw: response.cpm
            }, {
                creativeHTML: response.ad
            });
        }
    });
})();
