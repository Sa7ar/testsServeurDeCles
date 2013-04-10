var privateKeyFormToggle = true;
var publicKeyFormToggle = true;
var generateKeyFormToggle = true;
var importPublicKeyFormToggle = true;

function showMessages(msg){
    console.log(msg); 
}

function generateKeyPair(){
    $('.alert').hide();
    var form = $('#generateKeyPairForm');
    var keyPair = openpgp.generate_key_pair(1,parseInt(form.find('#numBits').val(), 10), form.find('#name').val() + ' <' + form.find('#email').val() + '>', form.find('#password').val());
    openpgp.keyring.importPrivateKey(keyPair.privateKeyArmored, form.find('#password').val());
    openpgp.keyring.importPublicKey(keyPair.publicKeyArmored);
    openpgp.keyring.store();
    parsePrivateKeys();
    parsePublicKeys();
}

function insertPrivateKey(){
    $('.alert').hide();
    var privKey = $('#newPrivateKey').val();
    var privKeyPassword = $('#newPrivateKeyPassword').val();
    try{
        if(openpgp.keyring.importPrivateKey(privKey, privKeyPassword)){
            openpgp.keyring.store();
            parsePrivateKeys();
            return true;
        }
        else{
            $('#insertPrivateKeyForm').prepend('<div class="alert alert-error" id="gCryptAlertPassword">Mymail-Crypt for Gmail was unable to read your key. Is your password correct?</div>');
        }
    }
    catch(e){
    }
    $('#insertPrivateKeyForm').prepend('<div class="alert alert-error" id="gCryptAlertPassword">Mymail-Crypt for Gmail was unable to read your key. It would be great if you could contact us so we can help figure out what went wrong.</div>');
    return false;
}

// Slightly modified
function insertPublicKey(pubKey){
    $('.alert').hide();
    // var pubKey = $('#newPublicKey').val();
    try{
        openpgp.keyring.importPublicKey(pubKey);
        openpgp.keyring.store();
        parsePublicKeys();
        return true;
    }
    catch(e){
    }
    $('#insertPublicKeyForm').prepend('<div class="alert alert-error" id="gCryptAlertPassword">Mymail-Crypt for Gmail was unable to read this key. It would be great if you could contact us so we can help figure out what went wrong.</div>');
    return false;
}

function parsePublicKeys(){
    var keys = openpgp.keyring.publicKeys;
    $('#publicKeyTable>tbody>tr').remove();
    for(var k=0;k<keys.length;k++){
        var key = keys[k];
        var user = gCryptUtil.parseUser(key.obj.userIds[0].text);
        $('#publicKeyTable>tbody').append('<tr><td class="removeLink" id="'+k+'"><a href="#">remove</a></td><td>'+user.userName+'</td><td>'+user.userEmail+'</td><td>'+util.hexstrdump(key.keyId)+'</td><td><a href="#public'+k+'" data-toggle="modal">show key</a><div class="modal" id="public'+k+'"><a href="#" class="close" data-dismiss="modal">Close</a><br/ >'+key.armored.replace(/\n/g,'<br/ >')+'</div></td></tr>');
        $('#public'+k).hide();
        $('#public'+k).modal({backdrop: true, show: false});
    }
    $('#publicKeyTable .removeLink').click(function(e){
        openpgp.keyring.removePublicKey(e.currentTarget.id);
        openpgp.keyring.store();
        parsePublicKeys();
    });
}

function parsePrivateKeys(){
    var keys = openpgp.keyring.privateKeys;
    $('#privateKeyTable>tbody>tr').remove();
    for(var k=0;k<keys.length;k++){
        var key = keys[k];
        var user = gCryptUtil.parseUser(key.obj.userIds[0].text);
        $('#privateKeyTable>tbody').append('<tr><td class="removeLink" id="'+k+'"><a href="#">remove</a></td><td>'+user.userName+'</td><td>'+user.userEmail+'</td><td><a href="#private'+k+'" data-toggle="modal">show key</a><div class="modal" id="private'+k+'"><a class="close" data-dismiss="modal">Close</a><br/ >'+key.armored.replace(/\n/g,'<br/ >')+'</div></td></tr>');
        $('#private'+k).hide();
        $('#private'+k).modal({backdrop: true, show: false});
    }
    $('#privateKeyTable .removeLink').click(function(e){
        openpgp.keyring.removePrivateKey(e.currentTarget.id);
        openpgp.keyring.store();
        parsePrivateKeys();
    });
}

/**
 * We use openpgp.config for storing our options.
 */
function saveOptions(){
    var gCryptSettings = openpgp.config.config.gCrypt;
    if(!gCryptSettings){
        gCryptSettings = {};
    }
    if($('#stopAutomaticDrafts:checked').length == 1){
        gCryptSettings.stopAutomaticDrafts = true;
    } else {
        gCryptSettings.stopAutomaticDrafts = false;
    }
    if($('#includeMyself:checked').length == 1){
        gCryptSettings.includeMyself = true;
    } else {
        gCryptSettings.includeMyself = false;
    }
    if($('#showComment:checked').length == 1){
        openpgp.config.config.show_comment = true;
    } else {
        openpgp.config.config.show_comment = false;
    }
    if($('#showVersion:checked').length == 1){
        openpgp.config.config.show_version = true;
    } else {
        openpgp.config.config.show_version = false;
    }

    openpgp.config.config.gCrypt = gCryptSettings;
    openpgp.config.write();
}

function loadOptions(){
    var gCryptSettings = openpgp.config.config.gCrypt;
    if (gCryptSettings && gCryptSettings.stopAutomaticDrafts){
        $('#stopAutomaticDrafts').attr('checked', true);
    }
    if (gCryptSettings && gCryptSettings.includeMyself) {
        $('#includeMyself').attr('checked', true);
    }
    if (openpgp.config.config.show_comment){
        $('#showComment').attr('checked', true);
    }
    if (openpgp.config.config.show_version){
        $('#showVersion').attr('checked', true);
    }
}

function linkLocalFunction(event){
    $('.alert').hide();
    $('span').hide();
    if(event && event.currentTarget){
        $(event.currentTarget.hash).show();
    }
}

function searchPubKeyOnServer(){
    // This function make the AJAX requests.
    function makeRequest(callback,request) {
        xhr = new XMLHttpRequest();
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4 ){
                if (xhr.status == 200 || xhr.status == 0) {
                    if(!xhr.responseText){
                        showMessages("****Erreur : responseText est vide.");
                    }
                    else{
                        showMessages("responseText : "+xhr.responseText);
                        callback(xhr.responseText);
                    }
                }
            }
        };
        showMessages("Sending a request");
        xhr.open("GET",request,true);
        xhr.send(null);
    }

    // After the first request, we get the KeiID associated with the email address
    // that the user entered.
    // For the moment, if several results are returned by gingerbear, this function
    // extract only the first KeyID it finds...
    function extractKeyId(htmlString){
        if( /(0x[a-z0-9]{16})/i.test(htmlString)){
            var keyID = RegExp.$1;
            showMessages("Key ID found : "+keyID);
            request = 'http://keyserver.gingerbear.net/pks/lookup?op=get&search='+keyID;
            makeRequest(extractPubKey,request);
        }
        else{
            showMessages("No Key ID found");
        }
    }


    // After the second request, the function extract the public key, and call insertPublicKey
    // to insert it in the keyring.
    function extractPubKey(htmlString){
        if(/(-----BEGIN PGP PUBLIC KEY BLOCK-----[\s\S]+?-----END PGP PUBLIC KEY BLOCK-----)/i.test(htmlString)){
            var pubKey = RegExp.$1;
            showMessages("Key found : "+pubKey);
            insertPublicKey(pubKey);
            showMessages("Key Inserted");
        }
    }
    // This function verify that the user entered a valid email address.
    function verifyEmailAddress(emailAddress){
        if(/[a-z0-9._-]+@[a-z0-9._-]+\.[a-z]{2,6}$/i.test(emailAddress)){
            console.log("Valid email address. We can make a request");
            // First request
            var request = 'http://keyserver.gingerbear.net/pks/lookup?search='+emailAddress+'&fingerprint=on&op=index';
            makeRequest(extractKeyId,request);
        }
        else{
            alert(emailAddress+" is not a valid address.");
        }
    }
    var request = 'http://keyserver.gingerbear.net/pks/lookup?search=corentinhenry@gmail.com&fingerprint=on&op=index';
    makeRequest(extractKeyId,request);
}

function onLoad(){
    openpgp.init();
    parsePrivateKeys();
    parsePublicKeys();
    loadOptions();
    $('.linkLocal').click(linkLocalFunction).click();
    $('#homeSpan').show();
    $('#generateKeyPairForm').hide();
    $('#generateKeyPairTitle').click(function() {
        $('#generateKeyPairForm').toggle(generateKeyFormToggle);
        generateKeyFormToggle = !generateKeyFormToggle;
    });      
    $('#insertPrivateKeyForm').hide();
    $('#insertPrivateKeyTitle').click(function() {
        $('#insertPrivateKeyForm').toggle(privateKeyFormToggle);
        privateKeyFormToggle = !privateKeyFormToggle;
    });
    $('#insertPublicKeyForm').hide();
    $('#insertPublicKeyTitle').click(function() {
        $('#insertPublicKeyForm').toggle(publicKeyFormToggle);
        publicKeyFormToggle = !publicKeyFormToggle;
    });
    // This is for the "import from key server" section
    $('#importPublicKeyForm').hide();
    $('#importPublicKeyTitle').click(function() {
        $('#importPublicKeyForm').toggle(publicKeyFormToggle);
        importPublicKeyFormToggle = !importPublicKeyFormToggle;
    });
    $('#optionsFormSubmit').click(saveOptions);
    $('#insertPrivateKeyFormSubmit').click(insertPrivateKey);
    $('#generateKeyPairFormSubmit').click(generateKeyPair);
    $('#insertPublicKeyFormSubmit').click(insertPublicKey($('#newPublicKey').val()));
    // This is for the "import from key server" section
    $('#importPublicKeyFormSubmit').click(searchPubKeyOnServer);
}

$(document).ready(onLoad());
