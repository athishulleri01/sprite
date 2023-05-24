(function () {
    var psConfig = PriceSpider.widgetConfigs["5ef123a9daf2681906e978a9"];

    var buttonTextKeys = [
        {
            key: ["shop now", "buy now", "buy online"],
            value: "findOnline"
        },
        {
            key: ["find nearby", "find near you", "find in store"],
            value: "findInStore"
        }
    ];

    //Variable used to determine which selector was chosen for sku change
    var lastSelectorClicked = "none";
    function createSelectorListener() {
        lastSelectorClicked = "none";
        //Adding event listeners to asign string data to know which selector was used to change sku later
        if (document.evaluate("//label[contains(text(),'B')]/following-sibling::select", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue) {
            var brandSelector = document.evaluate("//label[contains(text(),'B')]/following-sibling::select", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue;
            brandSelector.addEventListener("click", () => {
                lastSelectorClicked = "Brand";
            });
        }
        if (document.evaluate("//label[contains(text(),'F')]/following-sibling::select", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue) {
            var flavorSelector = document.evaluate("//label[contains(text(),'F')]/following-sibling::select", document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue;
            flavorSelector.addEventListener("click", () => {
                lastSelectorClicked = "Flavor";
            });
        }
    }

    //Analytics Setup START- IMP-32138

    psConfig.on("open", function (widget) {

        for (x = 0; x < PriceSpider.widgets.length; x++) {
            //Check which button was clicked and save the button label in the data
            //Do not apply open event to the WTB button in the header (pricespider-header class)
            if (PriceSpider.widgets[x].target[0].classList.contains("ps-open")) {
                if (!(PriceSpider.widgets[x].target[0].classList.contains("pricespider-header"))) {
                    PriceSpider.widgets[x].data.res.psButtonLabel = PriceSpider.widgets[x].target[0].getElementsByClassName("ps-button-label")[0].innerText;;

                    // Match button text with one of the button text keys
                    var buttonText = widget.data.res.psButtonLabel.toLowerCase();
                    var eventName = "findOnline";
                    for (i = 0; i < buttonTextKeys.length; i++) {
                        for (j = 0; j < buttonTextKeys[i].key.length; j++) {
                            if (buttonText == buttonTextKeys[i].key[j]) {
                                eventName = buttonTextKeys[i].value
                            }
                        }
                    }

                    //Send data in the datalayer
                    if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
                        //Online events - metric1
                        if (eventName == "findOnline") {
                            dataLayer.push({
                                'event': `${eventName}`,
                                'ecommerce': {
                                    'currencyCode': 'USD',
                                    'add': {
                                        'products': [{
                                            'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                            'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                            'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                            'variant': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                            'dimension25': 'pricespider',
                                            'dimension26': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                            'metric1': 1
                                        }]
                                    }
                                }
                            });
                        }
                        //Find in store events - metric2
                        else if (eventName == "findInStore") {
                            dataLayer.push({
                                'event': `${eventName}`,
                                'ecommerce': {
                                    'currencyCode': 'USD',
                                    'add': {
                                        'products': [{
                                            'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                            'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                            'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                            'variant': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                            'dimension26': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                            'metric2': 1
                                        }]
                                    }
                                }
                            });
                        }
                    }
                }

                // New event to track deduped product views on price spider
                if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
                    dataLayer.push({
                        'event': 'psProductView',
                        'ecommerce': {
                            'currencyCode': 'USD',
                            'add': {
                                'products': [{
                                    'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                    'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                    'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                    'metric18': 1
                                }]
                            }
                        }
                    });
                }
            }
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.open.length > 1) {
            psConfig.listeners.open.pop()
        }

        createSelectorListener();
    });
    psConfig.on("redirect", function (widget, productSeller) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            //Online Redirect
            if (productSeller instanceof PriceSpider.OnlineProductSeller) {
                dataLayer.push({
                    'event': 'psFindOnline',
                    'ecommerce': {
                        'currencyCode': 'USD',
                        'add': {
                            'products': [{
                                'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                'variant': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                'dimension25': `${productSeller.seller.name.toLowerCase()}`,
                                'dimension26': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                                'metric12': 1
                            }]
                        }
                    }
                });
            }
            // Local Redirect
            else {
                dataLayer.push({
                    'event': 'anchor-click',
                    'attributes': {
                        'origin': 'price-spider',
                        'region': 'lightbox',
                        'subCategory': 'ps-redirect-to-local-seller'
                    },
                    'eventInfo': {
                        'category': 'anchor',
                        'eventAction': 'click',
                        'eventName': 'ps-redirect-to-local-seller',
                        'value': 'null',
                        'metric14': 1
                    }
                });
            }
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.redirect.length > 1) {
            psConfig.listeners.redirect.pop()
        }
    });
    psConfig.on("locate", function (widget) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            dataLayer.push({
                'event': 'psFindInStore',
                'ecommerce': {
                    'currencyCode': 'USD',  // this does not have a mapping in their values, can it be added?
                    'add': {
                        'products': [{
                            'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                            'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                            'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                            'variant': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                            'dimension26': `${widget.data.sku.attributes["Package Size"].toLowerCase()}`,
                            'metric13': 1
                        }]
                    }
                }
            });
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.locate.length > 1) {
            psConfig.listeners.locate.pop()
        }
    });
    psConfig.on("getDirections", function (widget) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            dataLayer.push({
                'event': 'anchor-click',
                'attributes': {
                    'origin': 'price-spider',
                    'region': 'lightbox',
                    'subCategory': 'ps-get-directions'
                },
                'eventInfo': {
                    'category': 'anchor',
                    'eventAction': 'click',
                    'eventName': 'ps-get-directions',
                    'value': 'null',
                    'metric15': 1
                }
            });
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.getDirections.length > 1) {
            psConfig.listeners.getDirections.pop()
        }
    });
    psConfig.on("callStore", function (widget) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            dataLayer.push({
                'event': 'anchor-click',
                'attributes': {
                    'origin': 'price-spider',
                    'region': 'lightbox',
                    'subCategory': 'ps-call-store'
                },
                'eventInfo': {
                    'category': 'anchor',
                    'eventAction': 'click',
                    'eventName': 'ps-call-store',
                    'value': 'null',
                    'metric16': 1
                }
            });
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.callStore.length > 1) {
            psConfig.listeners.callStore.pop()
        }
    });
    psConfig.on("skuChanged", function (widget) {
        //Determining if either Brand selector was used or Flavor selector was used to change sku
        //Getting index of variable the user selected on option list
        //Then doing a dataLayer.push
        if (lastSelectorClicked == "Brand") {
            var brandOptions = document.evaluate("//label[contains(text(),'B')]/following-sibling::select/option", document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null);
            var brandAttribute = widget.data.sku.attributes["Brand Name"];
            var brandPosition = 0;
            var node = null;
            var optionNames = [];
            while (node = brandOptions.iterateNext()) {
                optionNames.push(node.textContent);
            }
            for (var i = 0; i < optionNames.length; i++) {
                if (brandAttribute == optionNames[i]) {
                    brandPosition = i + 1;
                    break;
                }
            }

            if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
                dataLayer.push({
                    'event': 'psProductClick',
                    'ecommerce': {
                        'currencyCode': 'USD',
                        'click': {
                            'actionField': {
                                'list': 'ps dropdown'
                            },
                            'products': [{
                                'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                'position': brandPosition,
                                'metric17': 1
                            }]
                        }
                    }
                });
            }
        }

        if (lastSelectorClicked == "Flavor") {
            var flavorOptions = document.evaluate("//label[contains(text(),'F')]/following-sibling::select/option", document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null);
            var flavorAttribute = widget.data.sku.attributes["Flavor"];
            var flavorPosition = 0;
            var node = null;
            var optionNames = [];
            while (node = flavorOptions.iterateNext()) {
                optionNames.push(node.textContent);
            }
            for (var t = 0; t < optionNames.length; t++) {
                if (flavorAttribute == optionNames[t]) {
                    flavorPosition = t + 1;
                    break;
                }
            }

            if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
                dataLayer.push({
                    'event': 'psProductClick',
                    'ecommerce': {
                        'currencyCode': 'USD',
                        'click': {
                            'actionField': {
                                'list': 'ps dropdown'
                            },
                            'products': [{
                                'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                                'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                                'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                                'position': flavorPosition,
                                'metric17': 1
                            }]
                        }
                    }
                });
            }
        }

        dataLayer.push({
            'event': 'psProductView',
            'ecommerce': {
                'currencyCode': 'USD',
                'add': {
                    'products': [{
                        'id': `${widget.data.sku.attributes["Sub Brand"].toLowerCase().replace(/ /g, "-")}`,
                        'name': `${widget.data.sku.attributes["Sub Brand"].toLowerCase()}`,
                        'brand': `${widget.data.sku.attributes["Type"].toLowerCase()}`,
                        'metric18': 1
                    }]
                }
            }
        });


        //Get rid of repeated event listeners
        while (psConfig.listeners.skuChanged.length > 1) {
            psConfig.listeners.skuChanged.pop()
        }

        createSelectorListener();
    });
    psConfig.on("onlineTab", function (widget) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            dataLayer.push({
                'event': 'tab-click',
                'attributes': {
                    'origin': 'price-spider',
                    'region': 'lightbox',
                    'subCategory': 'find-online'
                },
                'eventInfo': {
                    'category': 'tab',
                    'eventAction': 'ps-click',
                    'eventName': 'find-online',
                    'value': 'null'
                }
            });
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.onlineTab.length > 1) {
            psConfig.listeners.onlineTab.pop()
        }
    });
    psConfig.on("localTab", function (widget) {
        if (typeof dataLayer !== "undefined" && Array.isArray(dataLayer)) {
            dataLayer.push({
                'event': 'tab-click',
                'attributes': {
                    'origin': 'price-spider',
                    'region': 'lightbox',
                    'subCategory': 'find-nearby'
                },
                'eventInfo': {
                    'category': 'tab',
                    'eventAction': 'ps-click',
                    'eventName': 'find-nearby',
                    'value': 'null'
                }
            });
        }
        //Get rid of repeated event listeners
        while (psConfig.listeners.localTab.length > 1) {
            psConfig.listeners.localTab.pop()
        }
    });

    //Analytics Setup END- IMP-32138

})();
