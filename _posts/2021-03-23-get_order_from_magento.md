---
layout: post
title: How get Orders with customs attributes from magento Api
---

1.- Lo primero que debes hacer es exponer el extension_attribute en la api, para ello te invito a seguir los pasos indicados en  [este enlace](https://magento.stackexchange.com/a/236463).

2.- Para realizar una llamada a la API  desde un cliente, como una aplicación móvil, debe proporcionar un token de acceso en la llamada. El token actúa como una llave electrónica que le permite acceder a la API. Si aún no tienes un token puedes consultar  el procedimiento a seguir en la [documentacion oficial](https://devdocs.magento.com/guides/v2.4/get-started/authentication/gs-authentication-token.html).

3.- Ya se verifico que el extension_attribute esta expuesto en la API (para el ejemplo 'delivery_type')  y se obtuvo el TOKEN  (para el ejempl se asume que devolvió TOKEN-XXXXX), solo queda hacer la llamada API según el lenguaje que estamos usando, para ello se  indica la manera de hacerlo para PHP JavaScript y Python

Teniendo en cuenta que para el ejemplo vamos a obtener de la coleccion de ordenes solo los campos  "sore_id", "status", "delivery_type" por cada item ademas de la cantidad total de ordenes ("total_count"), se muestra el codigo necesario para hacer la llamada:

JavaScript - Jquery
_______________________________________________________________________________________________
var settings = {
  "url": "HOST/rest/V1/orders?searchCriteria[pageSize]=100&searchCriteria[currentPage]=1&fields=items[store_id,status,extension_attributes[delivery_type]],total_count",
  "method": "GET",
  "timeout": 0,
  "headers": {
    "Authorization": "Bearer TOKEN-XXXXX",
  },
};

$.ajax(settings).done(function (response) {
  console.log(response);
});

PHP - CURL
_________________________________________________________________________________________________

$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => 'HOST/rest/V1/orders?searchCriteria[pageSize]=100&searchCriteria[currentPage]=1&fields=items[store_id,status,extension_attributes[delivery_type]],total_count',
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => '',
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => array(
    'Authorization: Bearer TOKEN-XXXXX',
  ),
));

$response = curl_exec($curl);

curl_close($curl);
echo $response;

Pyhton - http.client
_________________________________________________________________________________________________
import http.client

conn = http.client.HTTPSConnection("HOST")
payload = ''
headers = {
  'Authorization': 'Bearer TOKEN-XXXXX',
}
conn.request("GET", "/rest/V1/orders?searchCriteria[pageSize]=100&searchCriteria[BcurrentPage]=1&fields=items[store_id,status,extension_attributes[delivery_type]],total_count", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))


Una vez procesada la petición se obtiene una respuesta como se muestra a continuación:

{
    "items": [
        {
            "store_id": "ABC-000000109",
            "status": "complete",
            "extension_attributes": {
                "delivery_type": "home"
            }
        },
        {
            "increment_id": "ABC-2000000040",
            "status": "pending",
            "extension_attributes": {
                "delivery_type": "work"
            }
        }
    ],
    "total_count": 2
}
