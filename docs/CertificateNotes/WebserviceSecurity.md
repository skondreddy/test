We provide a JKS for ERCOT and CAISO web service clients to sign Envelopes.
Do SQL Query to get alias for key to edit (Make sure query/key are for same desk/virtual desk)
Delete old key
Import new key (set password to out key password)
Save the keystore
    
Launch KeyStore Explorer again and ensure keys can be read
Verify that the subject username of the p12 matches that of the old
Deploy sesco.prod.jks to:

  * _J2ee Servers_ (/sesco/certs - As sescojobs)

  * _Job server_ (/home/sescojobs/certs - As sescojobs)

  * _Build server_ (/data/sescoapps/sesco/certs - As sescobuilder)

Deploy certificate file to:

  * _J2ee Servers_ (/sesco/certs - As sescojobs)

  * _Job server_ (/home/sescojobs/certs - As sescojobs)

  * _Build server_ (/data/sescoapps/sesco/certs - As sescobuilder)


Edit cert name in DB Config app
One under Jobs - Need to be careful to select correct one (Use your magic decoder key)
One under CoolApp (One per desk) 

ERCOT provides CA Root certificates for signing, ensure they are included in the JKS, otherwise we need to modify cacerts under Java and distribute to all active java installations
