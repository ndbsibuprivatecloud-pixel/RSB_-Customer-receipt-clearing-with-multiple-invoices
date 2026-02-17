# RSB_-Customer-receipt-clearing-with-multiple-invoices
Customer receipt clearing with multiple invoices
ZFI_037_CUST_REC_CLEARING_T1 Package 
Service Definitions 

 @EndUserText.label: 'Service definition for ZC_COSTCENTER'
define service ZUI_COSTCENTER_O4 {
  expose ZC_COSTCENTER;
}
*****************************************************************************

@EndUserText.label: 'Service definition for ZC_CUSTDRCR'
define service ZUI_CUSTDRCR_O4 {
  expose ZC_CUSTDRCR;
}

*****************************************************************************************

@EndUserText.label: 'Customer Receipt Header Details'
define service ZUI_CUSTRECHEAD_O2 {
  expose ZC_CUSTRECHEAD;
}

**********************************************************************************************

@EndUserText.label: 'Service definition for ZC_CUSTRECHEAD'
define service ZUI_CUSTRECHEAD_O4 {
  expose ZC_CUSTRECHEAD;
}
******************************************************************************************

@EndUserText.label: 'Service definition for ZC_CUSTRECITEM_M'
define service ZUI_CUSTRECITEM_O4 {
  expose ZC_CUSTRECITEM_M as CustRecItem;
}
*****************************************************************************************

@EndUserText.label: 'Service Defination for Upload invoice'
define service ZUI_POSTHEADER_O2 {
  expose ZC_PostHeader;
  expose ZC_POSTITEMS;
}
********************************************************************************************

@EndUserText.label: 'Customer Receipt Header Details'
define service ZUI_POSTNCLEAR_DOCUMENTS_O4 {
  expose ZC_PostNClear_Documents_M;
}
********************************************************************************************

Behaviour Definitions 

projection;
strict ( 2 );
use draft;

define behavior for ZC_COSTCENTER
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;
}
***************************************************************************************************************

projection;
strict ( 2 );
use draft;

define behavior for ZC_CUSTDRCR
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;
}

*******************************************************************************************************************

projection;
strict ( 2 );
use draft;

define behavior for ZC_CUSTRECHEAD
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;

  use action Uploaddocuments;
}

***************************************************************************

projection;
strict ( 2 );
use draft;

define behavior for ZC_CUSTRECITEM_M alias CustRecItem
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;
}
********************************************************************************************

projection;
strict ( 2 );
use draft;

define behavior for ZC_PostHeader //alias <alias_name>
{
  field ( readonly ) Receiptamount;
  use create;
  use update;
  use delete;

  use action uploadexceldata;
  use action upload;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;

  use association _items { create; with draft; }

  side effects
  {
    field Headeruuid affects entity _items;
  }
}


define behavior for ZC_POSTITEMS //alias <alias_name>
{
  //use update;
  use delete;
  use association _header { with draft; }
  // use association _header;
}
**********************************************************************************************************

projection;
strict ( 2 );

define behavior for ZC_PostNClear_Documents_M //alias <alias_name>
{
//  use create;
//  use update;
//  use delete;
  use action PostAndClear;
}
******************************************************************************************************

managed implementation in class ZBP_R_COSTCENTER unique;
strict ( 2 );
with draft;

define behavior for ZI_COSTCENTER
persistent table zcostcenter
draft table ZCOSTCENTER_D
etag master LocalInstanceLastChangedAt
lock master total etag LastChangedAt
authorization master( global )

{
  field ( mandatory : create )
   Profitcenter;

  field ( readonly )
   LastChangedAt,
   LastChangedBy,
   LocalInstanceLastChangedAt;

  field ( readonly : update )
   Profitcenter;


  create;
  update;
  delete;

  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  mapping for ZCOSTCENTER
  {
    Profitcenter = profitcenter;
    Costcenter = costcenter;
    LastChangedBy = last_changed_by;
    LastChangedAt = last_changed_at;
    LocalInstanceLastChangedAt = local_instance_last_changed_at;
  }
}
************************************************************************************************************************************

managed implementation in class ZBP_R_CUSTDRCR unique;
strict ( 2 );
with draft;

define behavior for ZI_CUSTDRCR
persistent table zcustdrcr
draft table zcustdrcrd
etag master LocalInstanceLastChangedAt
lock master total etag LastChangedAt
authorization master ( global )

{

  field ( numbering : managed )
  uuid;

  field ( mandatory : create )
  Companycode,
  Documentnumber,
  Postingdate,
  Postyear,
  ind;

  field ( readonly )
  uuid,
  LastChangedAt,
  LastChangedBy,
  LocalInstanceLastChangedAt;

  field ( readonly : update )
  Companycode,
  Documentnumber,
  Postingdate,
  Postyear,
  ind;

  create;
  update;
  delete;

  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  mapping for zcustdrcr
    {
      uuid                       = uuid;
      Companycode                = companycode;
      Documentnumber             = documentnumber;
      Postingdate                = postingdate;
      Postyear                   = postyear;
      ind                        = ind;
      Glaccount                  = glaccount;
      Customer                   = customer;
      DrCr                       = dr_cr;
      Amount                     = amount;
      LastChangedBy              = last_changed_by;
      LastChangedAt              = last_changed_at;
      LocalInstanceLastChangedAt = local_instance_last_changed_at;
    }
}
***********************************************************************************************************************************************

managed implementation in class ZBP_R_CUSTRECHEAD unique;
strict ( 2 );
with draft;

define behavior for ZI_CUSTRECHEAD
persistent table zcustrechead
draft table zcustrechead_d
etag master LocalInstanceLastChangedAt
lock master total etag LastChangedAt
authorization master ( global )

{
  field ( readonly )
  CreatedAt,
  CreatedBy,
  LastChangedAt,
  LastChangedBy,
  LocalInstanceLastChangedAt;

  field ( numbering : managed, readonly )
  HeaderUUID;

field ( mandatory )
  Profitcenter,
  Payor,
  Currency,
  Doctype,
  HousebankID,
  Bankaccount,
  Postingdate,
  Receiptdate,
  Receiptnumber;

  create;
  update;
  delete;

  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  validation MandateExchangerate on save { create; update; field Exchangerate, Profitcenter,Payor,
  Currency,Doctype,HousebankID,Bankaccount,Postingdate,Receiptdate,Receiptnumber; }

  side effects
  { field Currency affects field Exchangerate; }


  static action Uploaddocuments parameter ZI_Upload_Document;

  mapping for zcustrechead
    {
      HeaderUUID                 = headeruuid;
      Profitcenter               = profitcenter;
      Payor                      = payor;
      Customer                   = customer;
      Receiptnumber              = receiptnumber;
      Receiptamount              = receiptamount;
      Currency                   = currency;
      Doctype                    = doctype;
      HousebankID                = housebankid;
      Bankaccount                = bankaccount;
      Remarks                    = remarks;
      Exchangerate               = exchangerate;
      Postingdate                = postingdate;
      Receiptdate                = receiptdate;
      CreatedBy                  = created_by;
      CreatedAt                  = created_at;
      LastChangedBy              = last_changed_by;
      LastChangedAt              = last_changed_at;
      LocalInstanceLastChangedAt = local_instance_last_changed_at;
    }
}
********************************************************************************************************************************

managed implementation in class ZBP_I_CUSTRECITEM_M unique;
strict ( 2 );
with draft;

define behavior for ZI_CUSTRECITEM_M alias CustRecItem
persistent table zcustrecitem
draft table zcustrecitemd
etag master LocalInstanceLastChangedAt
lock master total etag LastChangedAt
authorization master ( global )

{
  field ( readonly )
  CreatedAt,
  CreatedBy,
  LastChangedAt,
  LastChangedBy,
  LocalInstanceLastChangedAt;
  field ( numbering : managed, readonly ) ItemUUID;

  create;
  update;
  delete;

  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  mapping for zcustrecitem
    {
      ItemUUID                   = itemuuid;
      HeaderUUID                 = headeruuid;
      Paymentamount              = paymentamount;
      ReferenceInvoiceno         = reference_invoiceno;
      Amountwriteoff             = amountwriteoff;
      WoReasonCd                 = wo_reason_cd;
      DeductionamountDn          = deductionamount_dn;
      ReasonCdDn                 = reason_cd_dn;
      DeductionDocumentNumber    = deduction_document_number;
      DeductionDocDate           = deduction_doc_date;
      TdsReceivable              = tds_receivable;
      TdsCode                    = tds_code;
      Remark                     = remark;
      clearingdocument           = Clearingdocument;
      partialfull                = Partialfull;
      error                      = error;
      CreatedBy                  = created_by;
      CreatedAt                  = created_at;
      LastChangedBy              = last_changed_by;
      LastChangedAt              = last_changed_at;
      LocalInstanceLastChangedAt = local_instance_last_changed_at;
    }
}
**********************************************************************************************************************************

managed implementation in class zbp_i_postheader unique;
strict ( 2 );
with draft;

define behavior for ZI_PostHeader alias Header
persistent table zcustrechead
lock master
total etag Headeruuid

draft table zcustrechead_d
authorization master ( instance )
etag master Headeruuid
{
  create;
  update;
  delete;

  field ( numbering : managed, readonly ) Headeruuid;

  field ( mandatory )
  Profitcenter,
  Payor,
  Currency,
  Doctype,
  HousebankID,
  Bankaccount,
  Postingdate,
  Receiptdate,
  Receiptnumber;

  field ( readonly )
  LocalInstanceLastChangedAt,
  LastChangedBy,
  CreatedBy,
  CreatedAt,
  LastChangedAt;

//  field ( readonly : update )
//  Receiptamount;

  //" Logic to convert uploaded excel into internal table and save to the child entity is written here
  action ( features : instance ) uploadexceldata result [1] $self;
  //  determination uploadexcel on modify { field Attachment; }
  action ( features : instance ) upload result [1] $self;
  //" association _ses_excel { create; with draft; }
  association _items { create ( features : instance ); with draft; }

  determination defaultvalue on modify { create; }
  //" Logic to trigger action uploadExcelData
  determination fields on modify { field Filename; }


  //" Change File Status During Creation of new record
  determination FillFileStatus on modify { field LocalInstanceLastChangedAt; }
  //" Change File Status When file is selected
  determination FillSelectedStatus on modify { field Attachment; }

  validation MandateExchangerate on save
  { create; update; field Exchangerate, Profitcenter, Payor,
    Currency, Doctype, HousebankID, Bankaccount, Postingdate, Receiptdate, Receiptnumber; }


  side effects

  { field Headeruuid affects entity _items;
    field Attachment affects field FileStatus, entity _items, field Receiptamount;
    action uploadExcelData affects $self, messages;
    field Currency affects field Exchangerate ; }//, field Profitcenter;
//    field Doctype affects field Profitcenter;  }

  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  mapping for zcustrechead
    {
      HeaderUUID                 = headeruuid;
      Profitcenter               = profitcenter;
      Payor                      = payor;
      Customer                   = customer;
      Receiptnumber              = receiptnumber;
      Receiptamount              = receiptamount;
      Currency                   = currency;
      Doctype                    = doctype;
      HousebankID                = housebankid;
      Bankaccount                = bankaccount;
      Remarks                    = remarks;
      Exchangerate               = exchangerate;
      Postingdate                = postingdate;
      Receiptdate                = receiptdate;
      attachment                 = attachment;
      mimetype                   = mimetype;
      filename                   = filename;
      CreatedBy                  = created_by;
      CreatedAt                  = created_at;
      LastChangedBy              = last_changed_by;
      LastChangedAt              = last_changed_at;
      LocalInstanceLastChangedAt = local_instance_last_changed_at;
    }
}

define behavior for ZI_Postitems alias items

implementation in class zbp_i_fi_postitems unique
persistent table zcustrecitem
draft table zcustrecitemd
//with unmanaged save
lock dependent by _header
authorization dependent by _header
//authorization master ( instance )
etag master Headeruuid
{
  //  update;
  //  delete;
  update ( features : instance );
  delete ( features : instance );

  field ( numbering : managed, readonly )
  Itemuuid;

  field ( readonly )
  headeruuid, paymentamount, referenceinvoiceno,
  amountwriteoff, woreasoncd, deductionamountdn, reasoncddn,
  deductiondocumentnumber, deductiondocdate, tdsreceivable,
  tdscode, remark, clearingdocument, partialfull, error, createdby,
  createdat, lastchangedby, lastchangedat, localinstancelastchangedat;

  association _header { with draft; }

  mapping for zcustrecitem
    {
      itemuuid                   = itemuuid;
      headeruuid                 = headeruuid;
      paymentamount              = paymentamount;
      referenceinvoiceno         = reference_invoiceno;
      amountwriteoff             = amountwriteoff;
      woreasoncd                 = wo_reason_cd;
      deductionamountdn          = deductionamount_dn;
      reasoncddn                 = reason_cd_dn;
      deductiondocumentnumber    = deduction_document_number;
      deductiondocdate           = deduction_doc_date;
      tdsreceivable              = tds_receivable;
      tdscode                    = tds_code;
      remark                     = remark;
      clearingdocument           = clearingdocument;
      partialfull                = partialfull;
      error                      = error;
      createdby                  = created_by;
      createdat                  = created_at;
      lastchangedby              = last_changed_by;
      lastchangedat              = last_changed_at;
      localinstancelastchangedat = local_instance_last_changed_at;
    }
}
********************************************************************************************************************************************

unmanaged implementation in class zbp_i_postnclear_documents_m unique;
strict ( 2 );

define behavior for ZI_PostNClear_Documents_M //alias <alias_name>
//late numbering
lock master
authorization master ( instance )
//etag master <field_name>
{
//  create;
//  update;
//  delete;

  action PostAndClear;

}

**********************************************************************************************************************************************

Data Definitions 

@AccessControl.authorizationCheck: #CHECK
@Metadata.allowExtensions: true
@EndUserText.label: 'Projection View for ZI_COSTCENTER'
@ObjectModel.semanticKey: [ 'Profitcenter' ]
define root view entity ZC_COSTCENTER
  provider contract transactional_query
  as projection on ZI_COSTCENTER
{
  key Profitcenter,
  Costcenter,
  LocalInstanceLastChangedAt
  
}
*****************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true
@EndUserText.label: 'Projection View for ZI_CUSTDRCR'
@ObjectModel.semanticKey: [ 'Uuid', 'Companycode', 'Documentnumber', 'Postingdate', 'Postyear', 'ind' ]
define root view entity ZC_CUSTDRCR
  provider contract transactional_query
  as projection on ZI_CUSTDRCR
{
  key uuid,
  key Companycode,
  key Documentnumber,
  key Postingdate,
  key Postyear,
  key ind,
  Glaccount,
  Customer,
  DrCr,
  Amount,
  LocalInstanceLastChangedAt
  
}
****************************************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true
@EndUserText.label: 'Projection View for ZI_CUSTRECHEAD'
define root view entity ZC_CUSTRECHEAD
  provider contract transactional_query
  as projection on ZI_CUSTRECHEAD
{
  key HeaderUUID,
  Profitcenter,
  Payor,
  Customer,
  Receiptnumber,
  Receiptamount,
  Currency,
  Doctype,
  HousebankID,
  Bankaccount,
  Remarks,
  Exchangerate,
  Postingdate,
  Receiptdate,
  LocalInstanceLastChangedAt
  
}
***************************************************************************************************************************

@AccessControl.authorizationCheck: #CHECK
@Metadata.allowExtensions: true
@EndUserText.label: 'Projection View for ZI_CUSTRECITEM_M'
define root view entity ZC_CUSTRECITEM_M
  provider contract transactional_query
  as projection on ZI_CUSTRECITEM_M
{
  key ItemUUID,
  key HeaderUUID,
      Paymentamount,
      ReferenceInvoiceno,
      Amountwriteoff,
      WoReasonCd,
      DeductionamountDn,
      ReasonCdDn,
      DeductionDocumentNumber,
      DeductionDocDate,
      TdsReceivable,
      TdsCode,
      Remark,
      @EndUserText.label : 'Clearing Document'
      Clearingdocument as Clearingdocument,
      @EndUserText.label : 'Partial-full Payment Indicator'
      Partialfull      as Partialfull,
      ERROR            as ERROR,
      @Semantics.user.createdBy: true
      LocalInstanceLastChangedAt

}
****************************************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post Header Details'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define root view entity ZC_PostHeader
  provider contract transactional_query
  as projection on ZI_PostHeader
{
  key Headeruuid,
      status,
      @EndUserText.label: 'Processing Status'
      Criticality,
      FileStatus,
      CriticalityStatus,
      HideExcel,
      Profitcenter,
      Payor,
      Customer,
      Receiptnumber,
      Receiptamount,
//      @Consumption.defaultValue: 'INR'
      Currency,
//      @Consumption.defaultValue: 'DZ'
      Doctype,
      Housebankid,
      Bankaccount,
      Remarks,
      Exchangerate,
      Postingdate,
      Receiptdate,
      Attachment,
      Mimetype,
      Filename,
      CreatedBy,
      CreatedAt,
      LastChangedBy,
      LastChangedAt,
      LocalInstanceLastChangedAt,
      /* Associations */
      _items : redirected to composition child ZC_POSTITEMS
}
*********************************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post Item Details'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true 
define view entity ZC_POSTITEMS as projection on ZI_POSTITEMS
{
    key Itemuuid,
    key Headeruuid,
    Paymentamount,
    ReferenceInvoiceno,
    Amountwriteoff,
    WoReasonCd,
    DeductionamountDn,
    ReasonCdDn,
    DeductionDocumentNumber,
    DeductionDocDate,
    TdsReceivable,
    TdsCode,
    Remark,
    Clearingdocument,
    Partialfull,
    Error,
    CreatedBy,
    CreatedAt,
    LastChangedBy,
    LastChangedAt,
    LocalInstanceLastChangedAt,
    /* Associations */
    _header  : redirected to parent ZC_PostHeader
}
*****************************************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post and Clearing Documents'
@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define root view entity ZC_PostNClear_Documents_M
  provider contract transactional_query
  as projection on ZI_PostNClear_Documents_M
{
  key Headeruuid,
  key Itemuuid,
      Profitcenter,
      Payor,
      Customer,
      Receiptnumber,
      Receiptamount,
      Currency,
      Doctype,
      Housebankid,
      Bankaccount,
      Remarks,
      Exchangerate,
      Postingdate,
      Receiptdate,
      Paymentamount,
      ReferenceInvoiceno,
      Amountwriteoff,
      WoReasonCd,
      DeductionamountDn,
      ReasonCdDn,
      DeductionDocumentNumber,
      DeductionDocDate,
      TdsReceivable,
      TdsCode,
      Remark,
      CreatedBy,
      CreatedAt,
      LastChangedBy,
      LastChangedAt,
      customercode,
      @Semantics.amount.currencyCode: 'Currency'
      grossamount,
      CustomerFullName,
      @Semantics.amount.currencyCode: 'Currency'
      openamount,
      validationcheck,
      Error,
      ClearingDocument,
      partialfull
      //LocalInstanceLastChangedAt
}
******************************************************************************************************************************

@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: '##GENERATED Mentain Cost Center'
define root view entity ZI_COSTCENTER
  as select from zcostcenter
{
  key profitcenter as Profitcenter,
  costcenter as Costcenter,
  @Semantics.user.lastChangedBy: true
  last_changed_by as LastChangedBy,
  @Semantics.systemDateTime.lastChangedAt: true
  last_changed_at as LastChangedAt,
  @Semantics.systemDateTime.localInstanceLastChangedAt: true
  local_instance_last_changed_at as LocalInstanceLastChangedAt
  
}
****************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '##GENERATED ZCUSTDRCR'
define root view entity ZI_CUSTDRCR
  as select from zcustdrcr
{
  key uuid                           as uuid,
  key companycode                    as Companycode,
  key documentnumber                 as Documentnumber,
  key postingdate                    as Postingdate,
  key postyear                       as Postyear,
  key ind                            as ind,
      glaccount                      as Glaccount,
      customer                       as Customer,
      dr_cr                          as DrCr,
      amount                         as Amount,
      @Semantics.user.lastChangedBy: true
      last_changed_by                as LastChangedBy,
      @Semantics.systemDateTime.lastChangedAt: true
      last_changed_at                as LastChangedAt,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      local_instance_last_changed_at as LocalInstanceLastChangedAt

}
************************************************************************************************************************************************

@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '##GENERATED Customer Clearing Header Data'
define root view entity ZI_CUSTRECHEAD
  as select from zcustrechead
{
  key headeruuid as HeaderUUID,
  profitcenter as Profitcenter,
  payor as Payor,
  customer as Customer,
  receiptnumber as Receiptnumber,
  receiptamount as Receiptamount,
  currency as Currency,
  doctype as Doctype,
  housebankid as HousebankID,
  bankaccount as Bankaccount,
  remarks as Remarks,
  exchangerate as Exchangerate,
  postingdate as Postingdate,
  receiptdate as Receiptdate,
  @Semantics.user.createdBy: true
  created_by as CreatedBy,
  @Semantics.systemDateTime.createdAt: true
  created_at as CreatedAt,
  @Semantics.user.lastChangedBy: true
  last_changed_by as LastChangedBy,
  @Semantics.systemDateTime.lastChangedAt: true
  last_changed_at as LastChangedAt,
  @Semantics.systemDateTime.localInstanceLastChangedAt: true
  local_instance_last_changed_at as LocalInstanceLastChangedAt
  
}
****************************************************************************************************************************************************************************

@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: '##GENERATED Cust Clr Documents - Upd'
define root view entity ZI_CUSTRECITEM_M
  as select from zcustrecitem as CustRecItem
{
  key itemuuid                       as ItemUUID,
  key headeruuid                     as HeaderUUID,
      paymentamount                  as Paymentamount,
      reference_invoiceno            as ReferenceInvoiceno,
      amountwriteoff                 as Amountwriteoff,
      wo_reason_cd                   as WoReasonCd,
      deductionamount_dn             as DeductionamountDn,
      reason_cd_dn                   as ReasonCdDn,
      deduction_document_number      as DeductionDocumentNumber,
      deduction_doc_date             as DeductionDocDate,
      tds_receivable                 as TdsReceivable,
      tds_code                       as TdsCode,
      remark                         as Remark,
      @EndUserText.label : 'Clearing Document'
      clearingdocument               as Clearingdocument,
      @EndUserText.label : 'Partial-full Payment Indicator'
      partialfull                    as Partialfull,
      @EndUserText.label : 'Error'
      error                          as ERROR,
      @Semantics.user.createdBy: true
      created_by                     as CreatedBy,
      @Semantics.systemDateTime.createdAt: true
      created_at                     as CreatedAt,
      @Semantics.user.lastChangedBy: true
      last_changed_by                as LastChangedBy,
      @Semantics.systemDateTime.lastChangedAt: true
      last_changed_at                as LastChangedAt,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      local_instance_last_changed_at as LocalInstanceLastChangedAt

}
********************************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Value Help For Bill From'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
@ObjectModel.dataCategory: #VALUE_HELP
@ObjectModel.representativeKey: 'Customer'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true
define view entity ZI_payorvh
    as select from I_CustomerCompany
{
      @ObjectModel.text.element: ['BPCustomerName']
      @Search: {
           defaultSearchElement: true,
           ranking: #HIGH,
           fuzzinessThreshold: 0.8
          }
      @UI.textArrangement: #TEXT_LAST
  key Customer,
      @ObjectModel.text.element: ['CompanyCodeName']
      @UI.textArrangement: #TEXT_LAST
      @Search: {
           defaultSearchElement: true,
           ranking: #MEDIUM,
           fuzzinessThreshold: 0.8
          }
  key CompanyCode,
      _Customer,
      _Customer.Country,
      _Customer.BPCustomerName,
      _CompanyCode.CompanyCodeName
}
where CompanyCode = '1000'

**************************************************************************************************************************************

//@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post Header Details'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
@ObjectModel.usageType:{
    serviceQuality: #A,
    sizeCategory: #XXL,
    dataClass: #TRANSACTIONAL 
    }
define root view entity ZI_PostHeader

  as select from zcustrechead as _header
  composition [0..*] of ZI_Postitems as _items
{
  key _header.headeruuid                                                                                       as Headeruuid,
      _header.status                                                                                           as status,
      case _header.status
                 when 'N' then 0
                 when 'F' then 1
                 when 'E' then 1
                 when 'S' then 3
                 else 0
               end                                                                                             as Criticality,
      cast( case when filename is initial and status is      initial then 'File Not Uploaded'
                when filename is not initial and  status is initial  then 'File Uploaded'
                when filename is initial then 'File Not Uploaded'
                when status is not initial then 'File Processed' else ' ' end as abap.char( 20 ) )             as FileStatus,
      cast( case when filename is initial and status is initial then '1'
                 when filename is not initial and  status is initial  then '2'
                 when filename is initial then '1'
                 when status is not initial then '3' else ' ' end as abap.char( 2 ) )                          as CriticalityStatus,

      cast( case when _header.filename is not initial then ' ' else 'X' end as abap_boolean preserving type  ) as HideExcel,

      _header.profitcenter                                                                                     as Profitcenter,
      _header.payor                                                                                            as Payor,
      _header.customer                                                                                         as Customer,
      _header.receiptnumber                                                                                    as Receiptnumber,
      _header.receiptamount                                                                                    as Receiptamount,
      _header.currency                                                                                         as Currency,
      _header.doctype                                                                                          as Doctype,
      _header.housebankid                                                                                      as Housebankid,
      _header.bankaccount                                                                                      as Bankaccount,
      _header.remarks                                                                                          as Remarks,
      _header.exchangerate                                                                                     as Exchangerate,
      _header.postingdate                                                                                      as Postingdate,
      _header.receiptdate                                                                                      as Receiptdate,
      @Semantics.largeObject:
            { mimeType: 'Mimetype',
              fileName: 'Filename',
             contentDispositionPreference: #INLINE }
      _header.attachment                                                                                       as Attachment,
      @Semantics.mimeType: true
      _header.mimetype                                                                                         as Mimetype,
      _header.filename                                                                                         as Filename,
      @Semantics.user.createdBy: true
      _header.created_by                                                                                       as CreatedBy,
      @Semantics.systemDateTime.createdAt: true
      _header.created_at                                                                                       as CreatedAt,
      @Semantics.user.lastChangedBy: true
      _header.last_changed_by                                                                                  as LastChangedBy,
      @Semantics.systemDateTime.lastChangedAt: true
      _header.last_changed_at                                                                                  as LastChangedAt,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      _header.local_instance_last_changed_at                                                                   as LocalInstanceLastChangedAt,
      _items
}
***********************************************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post Item Details'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
@ObjectModel.usageType:{
    serviceQuality: #A,
    sizeCategory: #XXL,
    dataClass: #TRANSACTIONAL
}
define view entity ZI_Postitems
  as select from zcustrecitem
  association to parent ZI_PostHeader as _header on $projection.Headeruuid = _header.Headeruuid
{
  key zcustrecitem.itemuuid                       as Itemuuid,
  key zcustrecitem.headeruuid                     as Headeruuid,
      zcustrecitem.paymentamount                  as Paymentamount,
      zcustrecitem.reference_invoiceno            as ReferenceInvoiceno,
      zcustrecitem.amountwriteoff                 as Amountwriteoff,
      zcustrecitem.wo_reason_cd                   as WoReasonCd,
      zcustrecitem.deductionamount_dn             as DeductionamountDn,
      zcustrecitem.reason_cd_dn                   as ReasonCdDn,
      zcustrecitem.deduction_document_number      as DeductionDocumentNumber,
      zcustrecitem.deduction_doc_date             as DeductionDocDate,
      zcustrecitem.tds_receivable                 as TdsReceivable,
      zcustrecitem.tds_code                       as TdsCode,
      zcustrecitem.remark                         as Remark,
      zcustrecitem.clearingdocument               as Clearingdocument,
      zcustrecitem.partialfull                    as Partialfull,
      zcustrecitem.error                          as Error,
      @Semantics.user.createdBy: true
      zcustrecitem.created_by                     as CreatedBy,
      @Semantics.systemDateTime.createdAt: true
      zcustrecitem.created_at                     as CreatedAt,
      @Semantics.user.lastChangedBy: true
      zcustrecitem.last_changed_by                as LastChangedBy,
      @Semantics.systemDateTime.lastChangedAt: true
      zcustrecitem.last_changed_at                as LastChangedAt,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      zcustrecitem.local_instance_last_changed_at as LocalInstanceLastChangedAt,

      _header
}
**************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Posting and Clearing details'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZI_PostNClear_documents
  as select from    zcustrechead             as _head
    inner join      zcustrecitem             as _item     on _head.headeruuid = _item.headeruuid
    left outer join ZI_AcctgCustomersDetails as _bsid     on _bsid.xblnr = _item.reference_invoiceno
    left outer join I_Customer               as _customer on _customer.Customer = _bsid.kunnr
    left outer join ZI_Accountingitemdetails as _acdoca   on  _acdoca.rebzg = _bsid.belnr
                                                          and _acdoca.rebzj = _bsid.gjahr

  // left outer join ZI_Accountingitemdetails as _acdoca   on _acdoca.xblnr = _item.reference_invoiceno
{

  key _head.headeruuid                                           as Headeruuid,
  key _item.itemuuid                                             as Itemuuid,
      _head.profitcenter                                         as Profitcenter,
      _head.payor                                                as Payor,
      _head.customer                                             as Customer,
      _head.receiptnumber                                        as Receiptnumber,
      _head.receiptamount                                        as Receiptamount,
      _head.currency                                             as Currency,
      _head.doctype                                              as Doctype,
      _head.housebankid                                          as Housebankid,
      _head.bankaccount                                          as Bankaccount,
      _head.remarks                                              as Remarks,
      _head.exchangerate                                         as Exchangerate,
      _head.postingdate                                          as Postingdate,
      _head.receiptdate                                          as Receiptdate,

      _item.paymentamount                                        as Paymentamount,
      _item.reference_invoiceno                                  as ReferenceInvoiceno,
      _item.amountwriteoff                                       as Amountwriteoff,
      _item.wo_reason_cd                                         as WoReasonCd,
      _item.deductionamount_dn                                   as DeductionamountDn,
      _item.reason_cd_dn                                         as ReasonCdDn,
      _item.deduction_document_number                            as DeductionDocumentNumber,
      _item.deduction_doc_date                                   as DeductionDocDate,
      _item.tds_receivable                                       as TdsReceivable,
      _item.tds_code                                             as TdsCode,
      _item.remark                                               as Remark,
      _item.created_by                                           as CreatedBy,
      _item.created_at                                           as CreatedAt,
      _item.last_changed_by                                      as LastChangedAt,
      _item.error                                                as Error,
      _item.clearingdocument                                     as ClearingDocument,
      _item.partialfull                                          as partialfull,
      // _item.local_instance_last_changed_at as LocalInstanceLastChangedAt,
      _bsid.kunnr                                                as customercode,
      @Semantics.amount.currencyCode: 'CURR'
      ////      _bsid.dmbtr                                                as grossamount,
      _bsid.wrbtr                                                as grossamount,
      _customer.CustomerFullName,
      _bsid.waers as CURR,
      @Semantics.amount.currencyCode: 'CURR'
      
      //      (  _bsid.dmbtr   - _acdoca.amount ) as openamount,
      ////      coalesce( cast( _bsid.dmbtr as abap.dec(23,2) ) ,0 ) -
      ////         coalesce( cast( _acdoca.amount as abap.dec(23,2) ) ,0 ) as openamount,

      coalesce( cast(  _bsid.wrbtr   as abap.dec(23,2) ) ,0 ) -
         coalesce( cast( _acdoca.amount as abap.dec(23,2) ) ,0 ) as openamount,


      case
      when ( coalesce( cast(_item.paymentamount as abap.dec(23,2)),0 ) +
            coalesce( cast(_item.amountwriteoff as abap.dec(23,2)),0 ) +
            coalesce( cast(_item.deductionamount_dn as abap.dec(23,2)),0 ) +
             coalesce( cast(_item.tds_receivable as abap.dec(23,2)),0 )
           =
              $projection.openamount )
      // ( cast(_bsid.dmbtr as abap.dec(23,2)) - cast(_acdoca.amount as abap.dec(23,2)) ) )
      then 'Yes'
      else 'No'
      end                                                        as validationcheck
      //  _item.partialfull
}
where
      _item.partialfull = ' '
  and _bsid.dmbtr       is not initial
// _item.clearingdocument is initial and 
**************************************************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Post and Clearing Documents'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #A,
    sizeCategory: #XXL,
    dataClass: #TRANSACTIONAL
}
define root view entity ZI_PostNClear_Documents_M
  as select from    zcustrechead             as _head
    inner join      zcustrecitem             as _item     on _head.headeruuid = _item.headeruuid
    left outer join ZI_AcctgCustomersDetails as _bsid     on _bsid.xblnr = _item.reference_invoiceno
    left outer join I_Customer               as _customer on _customer.Customer = _bsid.kunnr
    left outer join ZI_Accountingitemdetails as _acdoca   on  _acdoca.rebzg = _bsid.belnr
                                                          and _acdoca.rebzj = _bsid.gjahr
  //    left outer join ZI_Accountingitemdetails as _acdoca   on _acdoca.xblnr = _item.reference_invoiceno

{

  key _head.headeruuid                                        as Headeruuid,
  key _item.itemuuid                                          as Itemuuid,
      _head.profitcenter                                      as Profitcenter,
      _head.payor                                             as Payor,
      _head.customer                                          as Customer,
      _head.receiptnumber                                     as Receiptnumber,
      _head.receiptamount                                     as Receiptamount,
      _head.currency                                          as Currency,
      _head.doctype                                           as Doctype,
      _head.housebankid                                       as Housebankid,
      _head.bankaccount                                       as Bankaccount,
      _head.remarks                                           as Remarks,
      _head.exchangerate                                      as Exchangerate,
      _head.postingdate                                       as Postingdate,
      _head.receiptdate                                       as Receiptdate,
      _item.paymentamount                                     as Paymentamount,
      _item.reference_invoiceno                               as ReferenceInvoiceno,
      _item.amountwriteoff                                    as Amountwriteoff,
      _item.wo_reason_cd                                      as WoReasonCd,
      _item.deductionamount_dn                                as DeductionamountDn,
      _item.reason_cd_dn                                      as ReasonCdDn,
      _item.deduction_document_number                         as DeductionDocumentNumber,
      _item.deduction_doc_date                                as DeductionDocDate,
      _item.tds_receivable                                    as TdsReceivable,
      _item.tds_code                                          as TdsCode,
      _item.remark                                            as Remark,
      _item.created_by                                        as CreatedBy,
      _item.created_at                                        as CreatedAt,
      _item.last_changed_by                                   as LastChangedBy,
      _item.last_changed_at                                   as LastChangedAt,
      _item.error                                             as Error,
      _item.clearingdocument                                  as ClearingDocument,
      _item.partialfull                                       as partialfull,
      // _item.local_instance_last_changed_at as LocalInstanceLastChangedAt,
      _bsid.kunnr                                             as customercode,
 @Semantics.amount.currencyCode: 'CURR'
      ////      _bsid.dmbtr                                                as grossamount,
      _bsid.wrbtr                                                as grossamount,
   _bsid.waers as CURR,
      @Semantics.amount.currencyCode: 'CURR'
      //      cast(  _bsid.dmbtr  as abap.dec(23,2) ) - cast(  _acdoca.amount  as abap.dec(23,2) ) as openamount,
      //   @Semantics.amount.currencyCode: 'Currency'
      //  cast( (coalesce( _bsid.dmbtr, 0) - coalesce(_acdoca.amount, 0) as openamount,
      //  @Semantics.amount.currencyCode: 'Currency'
      // _acdoca.amount  as amt,

////      coalesce( cast( _bsid.dmbtr as abap.dec(23,2) ) ,0 ) -
////      coalesce( cast( _acdoca.amount as abap.dec(23,2) ) ,0 ) as openamount,

 coalesce( cast(  _bsid.wrbtr   as abap.dec(23,2) ) ,0 ) -
         coalesce( cast( _acdoca.amount as abap.dec(23,2) ) ,0 ) as openamount,

      case
      when ( coalesce( cast(_item.paymentamount as abap.dec(23,2)), 0 )  +
             coalesce( cast(_item.amountwriteoff as abap.dec(23,2)), 0 ) +
             coalesce( cast(_item.deductionamount_dn as abap.dec(23,2)), 0 ) +
             coalesce( cast(_item.tds_receivable as abap.dec(23,2)), 0 )
           =
           $projection.openamount )
      //( cast(_bsid.dmbtr as abap.dec(23,2) ) ),0 ) - cast(_acdoca.amount as abap.dec(23,2)) ) )
      then 'Yes'
      else 'No'
      end                                                     as validationcheck,
      _customer.CustomerFullName

}
where
      _item.partialfull = ' '
  and _bsid.dmbtr       is not initial
// _item.clearingdocument is initial and 
********************************************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Special GL Indicator Value help'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
@ObjectModel.dataCategory: #VALUE_HELP
@ObjectModel.representativeKey: 'SpecialGLCode'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true
define view entity ZI_SpecialGl_Vh
  as select from I_SpecialGLCode
    inner join   I_SpecialGLCodeText as _glText on  I_SpecialGLCode.FinancialAccountType = _glText.FinancialAccountType
                                                and I_SpecialGLCode.SpecialGLCode        = _glText.SpecialGLCode

{
@ObjectModel.text.element: ['SpecialGLCodeLongName']
      @Search: { defaultSearchElement: true,
                 ranking: #HIGH,
                 fuzzinessThreshold: 0.8 }
      @UI.textArrangement: #TEXT_LAST
  key I_SpecialGLCode.SpecialGLCode,
  key I_SpecialGLCode.FinancialAccountType,
  @Semantics.text: true
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #LOW
      @EndUserText.quickInfo: 'SpecialGLCodeLongName'
      _glText.SpecialGLCodeLongName

}
where
  _glText.Language = $session.system_language
*****************************************************************************************************************************************************

@EndUserText.label: 'Upload Clearing Documents'
define abstract entity ZI_Upload_Document

{
  MimeType    : abap.char(128);
  @EndUserText.label: 'File Name'
  FileName    : abap.char(128);
  FileContent : zfile_content;
}
***************************************************************************************************************************************************************

Metadata Extensions 

@Metadata.layer: #CORE
@UI: {
  headerInfo: {
    typeName: 'ZC_COSTCENTER', 
    typeNamePlural: 'ZC_COSTCENTERs'
  }
}
annotate view ZC_COSTCENTER with
{
  @UI.facet: [ {
    id: 'idIdentification', 
    type: #IDENTIFICATION_REFERENCE, 
    label: 'ZC_COSTCENTER', 
    position: 10 
  } ]
  @UI.lineItem: [ {
    position: 10 , 
    importance: #MEDIUM, 
    label: 'ProfitCenter'
  } ]
  @UI.identification: [ {
    position: 10 , 
    label: 'ProfitCenter'
  } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_ProfitcenterVH', element: 'ProfitCenter' }, useForValidation: true }]
  
  Profitcenter;
  
  @UI.lineItem: [ {
    position: 20 , 
    importance: #MEDIUM, 
    label: 'Cost Center'
  } ]
  @UI.identification: [ {
    position: 20 , 
    label: 'Cost Center'
  } ]
  
  Costcenter;
  
  @UI.hidden: true
  LocalInstanceLastChangedAt;
}
********************************************************************************************************************************************

@Metadata.layer: #CORE
@UI: {
  headerInfo: {
    typeName: 'ZC_CUSTDRCR', 
    typeNamePlural: 'ZC_CUSTDRCRs'
  }
}
annotate view ZC_CUSTDRCR with
{
  @UI.facet: [ {
    id: 'idIdentification', 
    type: #IDENTIFICATION_REFERENCE, 
    label: 'ZC_CUSTDRCR', 
    position: 10 
  } ]
  @UI.lineItem: [ {
    position: 10 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 10 , 
    label: ''
  } ]
  Companycode;
  
  @UI.lineItem: [ {
    position: 20 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 20 , 
    label: ''
  } ]
  Documentnumber;
  
  @UI.lineItem: [ {
    position: 30 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 30 , 
    label: ''
  } ]
  Postingdate;
  
  @UI.lineItem: [ {
    position: 40 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 40 , 
    label: ''
  } ]
  Postyear;
  
  @UI.lineItem: [ {
    position: 50 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 50 , 
    label: ''
  } ]
  Glaccount;
  
  @UI.lineItem: [ {
    position: 60 , 
    importance: #MEDIUM, 
    label: 'Customer'
  } ]
  @UI.identification: [ {
    position: 60 , 
    label: 'Customer'
  } ]
  Customer;
  
  @UI.lineItem: [ {
    position: 70 , 
    importance: #MEDIUM, 
    label: 'DrCr'
  } ]
  @UI.identification: [ {
    position: 70 , 
    label: 'DrCr'
  } ]
  DrCr;
  
  @UI.lineItem: [ {
    position: 80 , 
    importance: #MEDIUM, 
    label: 'Amount'
  } ]
  @UI.identification: [ {
    position: 80 , 
    label: 'Amount'
  } ]
  Amount;
  
  @UI.hidden: true
  LocalInstanceLastChangedAt;
}
******************************************************************************************************************************************

@Metadata.layer: #CORE
//@UI: {
//  headerInfo: {
//  title: {
//    value: 'Profitcenter',
//    type: #STANDARD
//  },
//  description:{
//    value: 'Profitcenter',
//    type: #STANDARD
//  },
//    typeName: 'Customer Upload Header Detail',
//    typeNamePlural: 'Customer Upload Header Details'
//  }
//}
@UI: {
  headerInfo: {
    typeName: 'Customer Upload Header Detail',
    typeNamePlural: 'Customer Upload Header Detail''s'
  }
}
annotate view ZC_CUSTRECHEAD with
{
  // @UI.facet: [
  //       {
  //       id:'iddet',
  //       purpose: #STANDARD,
  //       type: #COLLECTION,
  //       label: 'Customer Upload Header Detail',
  //       position: 10
  //     },
  //     {
  //    id: 'idIdentification',
  //    type: #IDENTIFICATION_REFERENCE,
  //    label: 'Customer Upload Header Detail',
  //    position: 10
  //  },
  //
  //     {
  //       label: 'Customer Upload Header Detail',
  //       id: 'idDetails',
  //       parentId: 'idIdentification',
  //       purpose: #STANDARD,
  //       type: #FIELDGROUP_REFERENCE,
  //       position: 20,
  //       targetQualifier: 'fgDetails'
  //     } ]
  @UI.facet: [ {
     id: 'idIdentification',
     type: #IDENTIFICATION_REFERENCE,
     label: 'Customer Upload Header Detail',
     position: 10
   },
   {  id     : 'idDetails',
                      parentId: 'idIdentification',
                      purpose: #STANDARD,
                      type   : #FIELDGROUP_REFERENCE,
                      label  : 'Details',
                      position: 20,
                      targetQualifier: 'fgDetails' } ]

  @UI.hidden: true
  @UI.lineItem: [ { position: 10 , importance: #MEDIUM, label: '' } ]
  @UI.identification: [ { position: 10 , label: '' } ]
  HeaderUUID;

  @UI.fieldGroup:     [ { position: 20, qualifier: 'fgDetails' } ]
  @UI:{
   lineItem: [ { position: 20, importance: #MEDIUM }, { type: #FOR_ACTION, dataAction: 'Uploaddocuments',
                                                         label: 'Upload Documents',
                                          semanticObjectAction: 'Display',
                                            invocationGrouping: #CHANGE_SET }],

   identification: [{ position: 20, label: 'Profitcenter'  } ] }//,{ type: #FOR_ACTION,
  //  dataAction: 'Uploaddocuments', label: 'Upload Documents' } ] }

  @UI.selectionField: [{ position: 20 }]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_ProfitcenterVH', element: 'ProfitCenter' }, useForValidation: true }]



  Profitcenter;
  @UI.fieldGroup:     [ { position: 30, qualifier: 'fgDetails' } ]
  @UI.selectionField: [{ position: 30 }]
  @UI.lineItem:       [ { position: 30 ,label: 'Payor', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 30 ,label: 'Payor' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @EndUserText.label: 'Payor'

  Payor;
  @UI.fieldGroup:     [ { position: 40, qualifier: 'fgDetails' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @UI.selectionField: [{ position: 40 }]
  @UI.lineItem:       [ { position: 40,label: 'Customer', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 40,label: 'Customer' } ]

  @EndUserText.label: 'Customer'

  Customer;
  @UI.fieldGroup        : [ { position: 50, qualifier: 'fgDetails' } ]
  @UI.selectionField: [{ position: 50 }]
  @UI.lineItem: [ { position: 50,label: 'Receipt Number', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 50,label: 'Receipt Number' } ]

  @EndUserText.label: 'Receipt Number'

  Receiptnumber;
  @UI.fieldGroup        : [ { position: 60, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 60,label: 'Receipt Amount', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 60,label: 'Receipt Amount' } ]

  @EndUserText.label: 'Receipt Amount'

  Receiptamount;
  @UI.fieldGroup        : [ { position: 70, qualifier: 'fgDetails' } ]
  @UI.selectionField: [{ position: 70 }]
  @UI.lineItem: [ { position: 70,label: 'Currency', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 70,label: 'Currency' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'I_CurrencyStdVH', element: 'Currency' }, useForValidation: true }]
  @EndUserText.label: 'Currency'

  Currency;

  @UI.selectionField: [{ position: 80 }]
  @UI.lineItem: [ { position: 80,label: 'Document Type', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 80 ,label: 'Document Type'} ]
  @UI.fieldGroup        : [ { position: 80, qualifier: 'fgDetails' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_DoctumentypeVH', element: 'Documenttype' }, useForValidation: true }]
  @EndUserText.label: 'Document Type'

  Doctype;
  @UI.fieldGroup        : [ { position: 90, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 90,label: 'Housebank ID', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 90,label: 'Housebank ID' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_HousebanckIDVh', element: 'HouseBankID' }, useForValidation: true }]
  @EndUserText.label: 'Housebank ID'

  HousebankID;
  @UI.fieldGroup        : [ { position: 100, qualifier: 'fgDetails' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_BankGLAccountVH', element: 'GLAccount' }, useForValidation: true }]
  @UI.lineItem: [ { position: 100 ,label: 'Bank Account', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 100 ,label: 'Bank Account' } ]

  @EndUserText.label: 'Bank Account'

  Bankaccount;
  @UI.fieldGroup        : [ { position: 110, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ {position: 110 ,label: 'Remarks', importance: #MEDIUM   } ]
  @UI.identification: [ { position: 110 ,label: 'Remarks' } ]
  @EndUserText.label: 'Remarks'

  Remarks;
  @UI.fieldGroup        : [ { position: 120, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 120 ,label: 'Exchangerate', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 120 ,label: 'Exchangerate'} ]
  @EndUserText.label: 'Exchangerate'

  Exchangerate;
  @UI.fieldGroup        : [ { position: 130, qualifier: 'fgDetails' } ]
  @UI.selectionField: [{ position: 130 }]
  @UI.lineItem: [ { position: 130 ,label: 'Posting Date', importance: #MEDIUM   } ]
  @UI.identification: [ { position: 130 ,label: 'Posting Date'} ]
  @EndUserText.label: 'Posting Date'

  Postingdate;
  @UI.fieldGroup        : [ { position: 140, qualifier: 'fgDetails' } ]
  @UI.selectionField: [{ position: 140 }]
  @UI.lineItem: [ { position: 140 ,label: 'Receipt Date', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 140 ,label: 'Receipt Date' } ]
  @EndUserText.label: 'Receipt Date'

  Receiptdate;

    @UI.hidden: true
    LocalInstanceLastChangedAt;
}
*****************************************************************************************************************************************

@Metadata.layer: #CORE
@UI: {
  headerInfo: {
    typeName: 'CustRecItem', 
    typeNamePlural: 'CustRecItems'
  }
}
annotate view ZC_CUSTRECITEM_M with
{
  @UI.facet: [ {
    id: 'idIdentification', 
    type: #IDENTIFICATION_REFERENCE, 
    label: 'CustRecItem', 
    position: 10 
  } ]
  @UI.hidden: true
  ItemUUID;
  
  @UI.lineItem: [ {
    position: 10 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 10 , 
    label: ''
  } ]
  HeaderUUID;
  
  @UI.lineItem: [ {
    position: 20 , 
    importance: #MEDIUM, 
    label: 'Paymentamount'
  } ]
  @UI.identification: [ {
    position: 20 , 
    label: 'Paymentamount'
  } ]
  Paymentamount;
  
  @UI.lineItem: [ {
    position: 30 , 
    importance: #MEDIUM, 
    label: 'ReferenceInvoiceno'
  } ]
  @UI.identification: [ {
    position: 30 , 
    label: 'ReferenceInvoiceno'
  } ]
  ReferenceInvoiceno;
  
  @UI.lineItem: [ {
    position: 40 , 
    importance: #MEDIUM, 
    label: 'Amountwriteoff'
  } ]
  @UI.identification: [ {
    position: 40 , 
    label: 'Amountwriteoff'
  } ]
  Amountwriteoff;
  
  @UI.lineItem: [ {
    position: 50 , 
    importance: #MEDIUM, 
    label: 'WoReasonCd'
  } ]
  @UI.identification: [ {
    position: 50 , 
    label: 'WoReasonCd'
  } ]
  WoReasonCd;
  
  @UI.lineItem: [ {
    position: 60 , 
    importance: #MEDIUM, 
    label: 'DeductionamountDn'
  } ]
  @UI.identification: [ {
    position: 60 , 
    label: 'DeductionamountDn'
  } ]
  DeductionamountDn;
  
  @UI.lineItem: [ {
    position: 70 , 
    importance: #MEDIUM, 
    label: 'ReasonCdDn'
  } ]
  @UI.identification: [ {
    position: 70 , 
    label: 'ReasonCdDn'
  } ]
  ReasonCdDn;
  
  @UI.lineItem: [ {
    position: 80 , 
    importance: #MEDIUM, 
    label: 'DeductionDocumentNumber'
  } ]
  @UI.identification: [ {
    position: 80 , 
    label: 'DeductionDocumentNumber'
  } ]
  DeductionDocumentNumber;
  
  @UI.lineItem: [ {
    position: 90 , 
    importance: #MEDIUM, 
    label: ''
  } ]
  @UI.identification: [ {
    position: 90 , 
    label: ''
  } ]
  DeductionDocDate;
  
  @UI.lineItem: [ {
    position: 100 , 
    importance: #MEDIUM, 
    label: 'TdsReceivable'
  } ]
  @UI.identification: [ {
    position: 100 , 
    label: 'TdsReceivable'
  } ]
  TdsReceivable;
  
  @UI.lineItem: [ {
    position: 110 , 
    importance: #MEDIUM, 
    label: 'TdsCode'
  } ]
  @UI.identification: [ {
    position: 110 , 
    label: 'TdsCode'
  } ]
  TdsCode;
  
  @UI.lineItem: [ {
    position: 120 , 
    importance: #MEDIUM, 
    label: 'Remark'
  } ]
  @UI.identification: [ {
    position: 120 , 
    label: 'Remark'
  } ]
  Remark;
  
  @UI.hidden: true
  LocalInstanceLastChangedAt;
}
*********************************************************************************************************

@Metadata.layer: #CORE
@EndUserText.label: 'Customer Receipt Upload'
@UI: {
 headerInfo : { typeName: 'Customer Receipt Upload' , typeNamePlural: 'Customer Receipt Upload Header Detail' ,
      title : { type: #STANDARD, label: 'Customer Receipt Upload Header Detail''s' } } }
annotate view ZC_PostHeader with
{
  @UI.facet: [ {
     id: 'idIdentification',
     type: #IDENTIFICATION_REFERENCE,
     label: 'Customer Upload Header Detail',
     position: 10 },

       /* Header Fecets and Datapoints */
  //       { purpose: #HEADER,
  //              id:'HDR_USER',
  //            type: #DATAPOINT_REFERENCE,
  //        position: 10,
  //  targetQualifier: 'Headeruuid' },

       { purpose: #HEADER,
              id:'HDR_FILE',
            type: #DATAPOINT_REFERENCE,
        position: 20,
  targetQualifier: 'LocalInstanceLastChangedAt' },

      { purpose: #HEADER,
             id:'HDR_STATUS',
           type: #DATAPOINT_REFERENCE,
       position: 30,
  targetQualifier: 'FileStatus' },


   //**----  Body facets
       { label: 'File Information',
            id: 'Attachment',
          type: #COLLECTION,
      position: 10 },

          {  id: 'Upload',
           type: #FIELDGROUP_REFERENCE,
       position: 20  ,
  targetQualifier: 'Upload',
       parentId: 'Attachment',
        purpose: #STANDARD },

   //** --- Excel data Facet **
       { label: 'Excel Data',
            id: 'Data',
          type: #LINEITEM_REFERENCE,
      position: 30,
  targetElement: '_items',
       purpose: #STANDARD } ]


  @UI: { lineItem:       [ { position: 10, importance: #HIGH , label: 'Headeruuid'}  ] ,
         identification: [ { position: 10 , label: 'Headeruuid' } ] }//,                       // { type: #FOR_ACTION, dataAction: 'upload', label: 'Download Excel Format'  }],
  //         dataPoint:        { title: 'Headeruuid', targetValueElement: 'Headeruuid' } }
  @UI.hidden: true
  Headeruuid;

  @UI: { lineItem:       [ { position: 20, importance: #HIGH , label: 'Processing Status'} ] ,
         identification: [ { position: 20 , label: 'Processing Status' } ],
         dataPoint:        { qualifier: 'FileStatus' , title: 'Processing Status', targetValueElement: 'FileStatus' } }

  FileStatus;

  @UI.fieldGroup: [ { position: 30 } ]
  @UI:{ lineItem: [ { position: 30, importance: #MEDIUM } ],
        identification: [{ position: 30, label: 'Profitcenter'  } ] }
  @UI.selectionField: [{ position: 30 }]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_ProfitcenterVH', element: 'ProfitCenter' }, useForValidation: true }]


  Profitcenter;
  @UI.fieldGroup:     [ { position: 40 } ]
  @UI.selectionField: [{ position: 40 }]
  @UI.lineItem:       [ { position: 40 ,label: 'Payor', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 40 ,label: 'Payor' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @EndUserText.label: 'Payor'
  Payor;
  @UI.fieldGroup:     [ { position: 50 } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @UI.selectionField: [{ position: 50 }]
  @UI.lineItem:       [ { position: 50,label: 'Customer', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 50,label: 'Customer' } ]

  @EndUserText.label: 'Customer'
  Customer;

  @UI.fieldGroup        : [ { position: 60 } ]
  @UI.selectionField: [{ position: 60 }]
  @UI.lineItem: [ { position: 60,label: 'Receipt Number', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 60,label: 'Receipt Number' } ]

  @EndUserText.label: 'Receipt Number'
  Receiptnumber;

    @UI.fieldGroup        : [ { position: 70 } ]
    @UI.lineItem: [ { position: 70,label: 'Receipt Amount', importance: #MEDIUM  } ]
    @UI.identification: [ { position: 70,label: 'Receipt Amount' } ]
  
    @EndUserText.label: 'Receipt Amount'
    Receiptamount;

  @UI.fieldGroup        : [ { position: 80 } ]
  @UI.selectionField: [{ position: 80 }]
  @UI.lineItem: [ { position: 80,label: 'Currency', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 80,label: 'Currency' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'I_CurrencyStdVH', element: 'Currency' }, useForValidation: true }]
  @EndUserText.label: 'Currency'
// @UI.defaultValue:  'INR'
 // @Consumption.defaultValue: 'INR'
  Currency;

  @UI.selectionField: [{ position: 90 }]
  @UI.lineItem: [ { position: 90,label: 'Document Type', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 90 ,label: 'Document Type'} ]
  @UI.fieldGroup        : [ { position: 90, qualifier: 'fgDetails' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_DoctumentypeVH', element: 'Documenttype'  }, useForValidation: true }]
  @EndUserText.label: 'Document Type'

  //@Consumption.defaultValue: 'DZ'

  Doctype;

  @UI.fieldGroup        : [ { position: 100 } ]
  @UI.lineItem: [ { position: 100,label: 'Housebank ID', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 100,label: 'Housebank ID' } ]

  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_HousebanckIDVh', element: 'HouseBankID' }, useForValidation: true }]
  @EndUserText.label: 'Housebank ID'

  Housebankid;

  @UI.fieldGroup        : [ { position: 110 } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_BankGLAccountVH', element: 'GLAccount' }, useForValidation: true }]
  @UI.lineItem: [ { position: 110 ,label: 'Bank Account', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 110 ,label: 'Bank Account' } ]

  @EndUserText.label: 'Bank Account'

  Bankaccount;

  @UI.fieldGroup        : [ { position: 120 } ]
  @UI.lineItem: [ {position: 120 ,label: 'Remarks', importance: #MEDIUM   } ]
  @UI.identification: [ { position: 120 ,label: 'Remarks' } ]
  @EndUserText.label: 'Remarks'

  Remarks;

  @UI.fieldGroup        : [ { position: 130 } ]
  @UI.lineItem: [ { position: 130 ,label: 'Exchangerate', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 130 ,label: 'Exchangerate'} ]
  @EndUserText.label: 'Exchangerate'


  Exchangerate;

  @UI.fieldGroup        : [ { position: 140 } ]
  @UI.selectionField: [{ position: 140 }]
  @UI.lineItem: [ { position: 140 ,label: 'Posting Date', importance: #MEDIUM   } ]
  @UI.identification: [ { position: 140 ,label: 'Posting Date'} ]
  @EndUserText.label: 'Posting Date'

  Postingdate;

  @UI.fieldGroup        : [ { position: 150 } ]
  @UI.selectionField: [{ position: 150 }]
  @UI.lineItem: [ { position: 150 ,label: 'Receipt Date', importance: #MEDIUM  } ]
  @UI.identification: [ { position: 150 ,label: 'Receipt Date' } ]
  @EndUserText.label: 'Receipt Date'

  Receiptdate;

  @UI: { fieldGroup:     [ { position: 160, qualifier: 'Upload' , label: 'Upload File' } ]}
  //  @UI: { identification: [ { position: 160 , label: 'File' } ] }

  Attachment;

  @UI.hidden: true

  Mimetype;
  @UI.hidden: true
  Filename;
  //  @UI.hidden: true
  //  CreatedBy;
  //  @UI.hidden: true
  //  CreatedAt;
  //  @UI.hidden: true
  //  LastChangedBy;
  //  @UI.hidden: true
  //  LastChangedAt;

  @UI: { dataPoint:{ title: 'LocalInstanceLastChangedAt', targetValueElement: 'LocalInstanceLastChangedAt' } }
  LocalInstanceLastChangedAt;

}
********************************************************************************************************************
@Metadata.layer: #CORE
annotate view ZC_POSTITEMS with
{
  @UI.facet: [{
     id: 'ReferenceInvoiceno',
     type: #IDENTIFICATION_REFERENCE,
     purpose: #STANDARD,
     label: 'Multiple Customer Receipt Upload'
  }]
  @UI.lineItem: [ { position: 1 , importance: #MEDIUM, label: '' } ]
  @UI.identification: [ { position: 1 , label: 'Itemuuid'} ]
  @UI.hidden: true
  Itemuuid;

  @UI.lineItem: [ { position: 2 , importance: #MEDIUM, label: '' } ]
  @UI.identification: [ { position: 2 , label: 'Headerid'} ]
  @UI.hidden: true
  Headeruuid;

 @UI.lineItem: [ { position: 10 , importance: #MEDIUM, label: 'Reference Invoiceno' } ]
 @UI.identification: [{ position: 10, label: 'Reference Invoiceno' }]
  
  ReferenceInvoiceno;
  @UI.lineItem: [ { position: 20 , importance: #MEDIUM, label: 'Payment Amount' } ]
  @UI.identification: [ { position: 20 , label: 'Payment Amount' } ]
  
  Paymentamount;
  @UI.lineItem: [ { position: 30 , importance: #MEDIUM, label: 'Amount Write Off' } ]
  @UI.identification: [ { position: 30 , label: 'Amount Write Off' } ]
  Amountwriteoff;
  @UI.lineItem: [ { position: 40 , importance: #MEDIUM, label: 'WO Reason CD    ' } ]
  @UI.identification: [ { position: 40 , label: 'WO Reason CD' } ]
  WoReasonCd;
  @UI.lineItem: [ { position: 50 , importance: #MEDIUM, label: 'D/N Deduction Amount' } ]
  @UI.identification: [ { position: 50 , label: 'D/N Deduction Amount' } ]
  DeductionamountDn;
  @UI.lineItem: [ { position: 60 , importance: #MEDIUM, label: 'D/N Reason CD' } ]
  @UI.identification: [ { position: 60 , label: 'D/N Reason CD' } ]
  ReasonCdDn;
  @UI.lineItem: [ { position: 70 , importance: #MEDIUM, label: 'Deduction Document Number' } ]
  @UI.identification: [ { position: 70 , label: 'Deduction Document Number' } ]
  DeductionDocumentNumber;
  @UI.lineItem: [ { position: 80 , importance: #MEDIUM, label: 'Deduction Document Date' } ]
  @UI.identification: [ { position: 80 , label: 'Deduction Document Date' } ]
  DeductionDocDate;
  @UI.lineItem: [ { position: 90 , importance: #MEDIUM, label: 'Tds Receivable' } ]
  @UI.identification: [ { position: 90 , label: 'Tds Receivable' } ]
  TdsReceivable;
  @UI.lineItem: [ { position: 100 , importance: #MEDIUM, label: 'Tds Code' } ]
  @UI.identification: [ { position: 100 , label: 'Tds Code' } ]
  TdsCode;
  @UI.lineItem: [ { position: 110 , importance: #MEDIUM, label: 'Remark' } ]
  @UI.identification: [ { position: 110 , label: 'Remark' } ]
  Remark;
  @UI.lineItem: [ { position: 120 , importance: #MEDIUM, label: 'Clearing Document' } ]
  @UI.identification: [ { position: 120 , label: 'Clearing Document' } ]
  Clearingdocument;
//  @UI.lineItem: [ { position: 20 , importance: #MEDIUM, label: 'Amountwriteoff' } ]
//  @UI.identification: [ { position: 20 , label: 'Amountwriteoff' } ]
//  Partialfull;
  @UI.lineItem: [ { position: 130 , importance: #MEDIUM, label: 'Results' } ]
  @UI.identification: [ { position: 130 , label: 'Results' } ]
  Error;
  @UI.lineItem: [ { position: 140, importance: #MEDIUM, label: 'Created By' } ]
  @UI.identification: [ { position: 140 , label: 'Created By' } ]
  CreatedBy;
  @UI.lineItem: [ { position: 150, importance: #MEDIUM, label: 'Created At' } ]
  @UI.identification: [ { position: 150, label: 'Created At' } ]
  CreatedAt;
//  LastChangedBy;
//  LastChangedAt;
//  LocalInstanceLastChangedAt;
//  /* Associations */
//  _header;
  

}
*****************************************************************************************************************************************

@Metadata.layer: #CORE
@UI: {
  headerInfo: {
    typeName: 'Customer receipt clearing with multi inv',
    typeNamePlural: 'Customer receipt clearing multi inv''s'
  }
}
annotate view ZC_PostNClear_Documents_M with
{
  @UI.facet: [ {
    id: 'idIdentification',
    type: #IDENTIFICATION_REFERENCE,
    label: 'Customer receipt clearing with multi inv',
    position: 10
  },
  {  id     : 'idProfitcenter',
                     parentId: 'idIdentification',
                     purpose: #STANDARD,
                     type   : #FIELDGROUP_REFERENCE,
                     label  : 'Profitcenter',
                     position: 20,
                     targetQualifier: 'fgDetails' } ]

  //@UI.hidden: true
  @UI.selectionField: [{ position: 90 }]
  Headeruuid;
  @UI.lineItem: [ { position: 10 , importance: #MEDIUM, label: '' } ]
  @UI.identification: [ { position: 10 , label: '' } ]
  @UI.hidden: true
  Itemuuid;
  @UI.lineItem: [ { position: 20 , importance: #MEDIUM, label: 'Profitcenter' } ,

                    { type: #FOR_ACTION, dataAction: 'PostAndClear',
                      label: 'PostAndClear',semanticObjectAction: 'Display',
                      invocationGrouping: #CHANGE_SET } ]

  @UI.fieldGroup        : [ { position: 20, qualifier: 'fgDetails' } ]
  @UI.identification: [ { position: 20 , label: 'Profitcenter' } ]
  @UI.selectionField: [{ position: 20 }]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_ProfitcenterVH', element: 'ProfitCenter' }, useForValidation: true }]
  Profitcenter;
  //  @UI.fieldGroup        : [ { position: 30, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 30 , importance: #MEDIUM, label: 'Payor' } ]
  //  @UI.identification: [ { position: 30 , label: 'Payor' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @UI.selectionField: [{ position: 30 }]
  @EndUserText.label: 'Payor'
  Payor;
  //  @UI.fieldGroup        : [ { position: 40, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 40 , importance: #MEDIUM, label: 'Customer' } ]
  //  @UI.identification: [ { position: 40 , label: 'Customer' } ]
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_payorvh', element: 'Customer' }, useForValidation: true }]
  @UI.selectionField: [{ position: 40 }]
  @EndUserText.label: 'Customer'
  Customer;
  //  @UI.fieldGroup        : [ { position: 50, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 50 , importance: #MEDIUM, label: 'Receiptnumber' } ]
  //  @UI.identification: [ { position: 50 , label: 'Receiptnumber' } ]
  //
  //  Receiptnumber;
  //  @UI.fieldGroup        : [ { position: 60, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 60 , importance: #MEDIUM, label: 'Receipt Amount' } ]
  //  @UI.identification: [ { position: 60 , label: 'Receipt Amount' } ]
  //
  //  Receiptamount;
  //  @UI.fieldGroup        : [ { position: 70, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 70 , importance: #MEDIUM, label: 'Currency' } ]
  //  @UI.identification: [ { position: 70 , label: 'Currency' } ]
  //
  //  Currency;
  //  @UI.fieldGroup        : [ { position: 80, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 80 , importance: #MEDIUM, label: 'Document Type' } ]
  //  @UI.identification: [ { position: 80 , label: 'Document Type' } ]
  //
  //  Doctype;
  //  @UI.fieldGroup        : [ { position: 90, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 90 , importance: #MEDIUM, label: 'Housebankid' } ]
  //  @UI.identification: [ { position: 90 , label: 'Housebankid' } ]
  //
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_HousebanckIDVh', element: 'HouseBankID' }, useForValidation: true }]
  @EndUserText.label: 'Housebank ID'
  @UI.selectionField: [{ position: 50 }]

  Housebankid;
  //  @UI.fieldGroup        : [ { position: 100, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 100 , importance: #MEDIUM, label: 'Bankaccount' } ]
  //  @UI.identification: [ { position: 100 , label: 'Bankaccount' } ]
  //
  //  Bankaccount;
  //  @UI.fieldGroup        : [ { position: 110, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 110 , importance: #MEDIUM, label: 'Header Remarks' } ]
  //  @UI.identification: [ { position: 110 , label: 'Header Remarks' } ]
  //
  //  Remarks;
  //  @UI.fieldGroup        : [ { position: 120, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 120 , importance: #MEDIUM, label: 'Exchangerate' } ]
  //  @UI.identification: [ { position: 120 , label: 'Exchangerate' } ]

  //  Exchangerate;

  //  @UI.fieldGroup        : [ { position: 140, qualifier: 'fgDetails' } ]
  //  @UI.lineItem: [ { position: 140 , importance: #MEDIUM, label: 'Receiptdate' } ]
  //  @UI.identification: [ { position: 140 , label: 'Receiptdate' } ]

  //  Receiptdate;
  @UI.fieldGroup        : [ { position: 30, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 30 , importance: #MEDIUM, label: 'Customer Code' } ]
  @UI.identification: [ { position: 30 , label: 'Customer Code' } ]
  customercode;
  @UI.fieldGroup        : [ { position: 40, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 40 , importance: #MEDIUM, label: 'Customer Full Name' } ]
  @UI.identification: [ { position: 40 , label: 'Customer Full Name' } ]
  CustomerFullName;
  @UI.fieldGroup        : [ { position: 50, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 50 , importance: #MEDIUM, label: 'Gross Amount' } ]
  @UI.identification: [ { position: 50 , label: 'Gross Amount' } ]
  grossamount;
  @UI.fieldGroup        : [ { position: 60, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 60 , importance: #MEDIUM, label: 'Open Amount' } ]
  @UI.identification: [ { position: 60 , label: 'Open Amount' } ]
  openamount;

  @UI.fieldGroup        : [ { position: 70, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 70 , importance: #MEDIUM, label: 'validation Check' } ]
  @UI.identification: [ { position: 70 , label: 'validationcheck' } ]
  validationcheck;


  @UI.fieldGroup        : [ { position: 80, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 80 , importance: #MEDIUM, label: 'Payment Amount' } ]
  @UI.identification: [ { position: 80 , label: 'Paymentamount' } ]

  Paymentamount;
  @UI.fieldGroup        : [ { position: 90, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 90 , importance: #MEDIUM, label: 'Reference Invoiceno' } ]
  @UI.identification: [ { position: 90 , label: 'ReferenceInvoiceno' } ]

  ReferenceInvoiceno;
  @UI.fieldGroup        : [ { position: 100, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 100 , importance: #MEDIUM, label: 'Amountwriteoff' } ]
  @UI.identification: [ { position: 100 , label: 'Amountwriteoff' } ]

  Amountwriteoff;
  @UI.fieldGroup        : [ { position: 110, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 110 , importance: #MEDIUM, label: 'WO Reason CD' } ]
  @UI.identification: [ { position: 110 , label: 'WO Reason CD' } ]

  WoReasonCd;
  @UI.fieldGroup        : [ { position: 120, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 120 , importance: #MEDIUM, label: 'D/N Deduction Amount' } ]
  @UI.identification: [ { position: 120 , label: 'D/N Deduction Amount' } ]

  DeductionamountDn;
  @UI.fieldGroup        : [ { position: 130, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 130 , importance: #MEDIUM, label: 'D/N Reason CD' } ]
  @UI.identification: [ { position: 130 , label: 'D/N Reason CD' } ]

  ReasonCdDn;
  @UI.fieldGroup        : [ { position: 140, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 140 , importance: #MEDIUM, label: 'Deduction Document Number' } ]
  @UI.identification: [ { position: 140 , label: 'Deduction Document Number' } ]

  DeductionDocumentNumber;
  @UI.fieldGroup        : [ { position: 150, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 150 , importance: #MEDIUM, label: 'Deduction Doc.Date' } ]
  @UI.identification: [ { position: 150 , label: 'Deduction Doc.Date' } ]

  DeductionDocDate;
  @UI.fieldGroup        : [ { position: 160, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 160 , importance: #MEDIUM, label: 'Tds Receivable' } ]
  @UI.identification: [ { position: 160 , label: 'Tds Receivable' } ]
  TdsReceivable;
  @UI.fieldGroup        : [ { position: 170, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 170 , importance: #MEDIUM, label: 'TDS Code' } ]
  @UI.identification: [ { position: 170 , label: 'TDS Code' } ]

  TdsCode;
  @UI.fieldGroup        : [ { position: 180, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 180 , importance: #MEDIUM, label: 'Item Remark' } ]
  @UI.identification: [ { position: 180 , label: 'Item Remark' } ]

  Remark;

  @UI.fieldGroup        : [ { position: 190, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 190 , importance: #MEDIUM, label: 'CreatedBy' } ]
  @UI.identification: [ { position: 190 , label: 'CreatedBy' } ]
  @UI.selectionField: [{ position: 190 }]
  CreatedBy;
  @UI.fieldGroup        : [ { position: 200, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 200 , importance: #MEDIUM, label: 'CreatedAt' } ]
  @UI.identification: [ { position: 200 , label: 'CreatedAt' } ]

  CreatedAt;
  @UI.fieldGroup        : [ { position: 210, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 210 , importance: #MEDIUM, label: 'Postingdate' } ]
  @UI.identification: [ { position: 210 , label: 'Postingdate' } ]
  @UI.selectionField: [{ position: 210 }]
  @EndUserText.label: 'Posting Date'
  Postingdate;
  
  @UI.fieldGroup        : [ { position: 220, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 220 , importance: #MEDIUM, label: 'Results' } ]
  @UI.identification: [ { position: 220 , label: 'Results' } ]
  @UI.selectionField: [{ position: 220 }]
  @EndUserText.label: 'Results'
  
  Error;
  
  @UI.fieldGroup        : [ { position: 230, qualifier: 'fgDetails' } ]
  @UI.lineItem: [ { position: 230 , importance: #MEDIUM, label: 'Clearing Document' } ]
  @UI.identification: [ { position: 230 , label: 'Clearing Document' } ]
  @UI.selectionField: [{ position: 230 }]
  @EndUserText.label: 'Clearing Document'
  
  ClearingDocument;
 
}
********************************************************************************************************************************************

Database Tables 

@EndUserText.label : 'Mantain Costcenter Based on profit Center'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcostcenter {

  key businesskey : include zcostcenter_key not null;
  businessdata    : include zcostcenter_data;
  admindata       : include zcostcenter_admin_data;

}
******************************************************************************

@EndUserText.label : '##GENERATED Mentain Cost Center'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcostcenter_d {

  key mandt                  : mandt not null;
  key profitcenter           : prctr not null;
  costcenter                 : kostl;
  lastchangedby              : abp_lastchange_user;
  lastchangedat              : abp_lastchange_tstmpl;
  localinstancelastchangedat : abp_locinst_lastchange_tstmpl;
  "%admin"                   : include sych_bdl_draft_admin_inc;

}
***********************************************************************************************

@EndUserText.label : 'Customer Entry Posting Details'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustdrcr {

  key businesskey : include zcustdrcr_key not null;
  businessdata    : include zcustdrcr_data;
  admindata       : include zcustdrcr_admin_data;

}
****************************************************************************************************************

@EndUserText.label : '##GENERATED ZCUSTDRCR'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustdrcrd {

  key mandt                  : mandt not null;
  key uuid                   : sysuuid_x16 not null;
  key companycode            : bukrs not null;
  key documentnumber         : belnr_d not null;
  key postingdate            : budat not null;
  key postyear               : gjahr not null;
  key ind                    : abap.char(1) not null;
  glaccount                  : hkont;
  customer                   : abap.char(10);
  drcr                       : abap.char(10);
  amount                     : abap.dec(23,2);
  lastchangedby              : abp_lastchange_user;
  lastchangedat              : abp_lastchange_tstmpl;
  localinstancelastchangedat : abp_locinst_lastchange_tstmpl;
  "%admin"                   : include sych_bdl_draft_admin_inc;

}
*******************************************************************************************************

@EndUserText.label : 'Customer receipt clearing with multiple inv Header Details'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustrechead {

  key businesskey : include zcustrechead_key not null;
  businessdata    : include zcustrechead_data;
  admindata       : include zcustrechead_admin_data;

}
**********************************************************************************************************

@EndUserText.label : '##GENERATED Customer Clearing Header Data'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustrechead_d {

  key mandt                  : mandt not null;
  key headeruuid             : sysuuid_x16 not null;
  profitcenter               : prctr;
  payor                      : abap.char(10);
  customer                   : abap.char(10);
  receiptnumber              : abap.char(20);
  receiptamount              : abap.dec(15,2);
  currency                   : abap.cuky;
  doctype                    : abap.char(2);
  housebankid                : abap.char(5);
  bankaccount                : abap.char(10);
  remarks                    : abap.char(50);
  exchangerate               : abap.dec(10,4);
  postingdate                : bldat;
  receiptdate                : budat;
  attachment                 : zfile_content;
  mimetype                   : abap.char(128);
  filename                   : abap.char(128);
  criticality                : abap.int1;
  filestatus                 : abap.char(20);
  criticalitystatus          : abap.char(2);
  hideexcel                  : abap_boolean;
  status                     : abap.char(1);
  createdby                  : abp_creation_user;
  createdat                  : abp_creation_tstmpl;
  lastchangedby              : abp_lastchange_user;
  lastchangedat              : abp_lastchange_tstmpl;
  localinstancelastchangedat : abp_locinst_lastchange_tstmpl;
  "%admin"                   : include sych_bdl_draft_admin_inc;

}

**************************************************************************************************************************

@EndUserText.label : 'Customer receipt clearing with multiple inv Item Details'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustrecitem {

  key businesskey : include zcustrecitem_key not null;
  businessdata    : include zcustrecitem_data;
  admindata       : include zcustrecitem_admin_data;

}

***************************************************************************************

@EndUserText.label : '##GENERATED Cust Clr Documents - Upd'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zcustrecitemd {

  key mandt                  : mandt not null;
  key itemuuid               : sysuuid_x16 not null;
  key headeruuid             : sysuuid_x16 not null;
  paymentamount              : abap.dec(23,2);
  referenceinvoiceno         : abap.char(16);
  amountwriteoff             : abap.dec(23,2);
  woreasoncd                 : abap.char(2);
  deductionamountdn          : abap.dec(23,2);
  reasoncddn                 : abap.char(250);
  deductiondocumentnumber    : abap.char(12);
  deductiondocdate           : datum;
  tdsreceivable              : abap.dec(23,2);
  tdscode                    : abap.char(250);
  remark                     : abap.char(250);
  clearingdocument           : belnr_d;
  @EndUserText.label : 'Document Status(P/F)'
  partialfull                : abap.char(1);
  @EndUserText.label : 'Errors'
  error                      : abap.char(300);
  createdby                  : abp_creation_user;
  createdat                  : abp_creation_tstmpl;
  lastchangedby              : abp_lastchange_user;
  lastchangedat              : abp_lastchange_tstmpl;
  localinstancelastchangedat : abp_locinst_lastchange_tstmpl;
  "%admin"                   : include sych_bdl_draft_admin_inc;

}

****************************************************************************************************************

Structures 

@EndUserText.label : 'Admin Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcostcenter_admin_data {

  last_changed_by                : abp_lastchange_user;
  last_changed_at                : abp_lastchange_tstmpl;
  local_instance_last_changed_at : abp_locinst_lastchange_tstmpl;

}
*********************************************************************************************************************

@EndUserText.label : 'Data Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcostcenter_data {

  costcenter : kostl;

}

********************************************************************************************************************

@EndUserText.label : 'Key Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcostcenter_key {

  key client       : mandt;
  key profitcenter : prctr;

}

******************************************************************************************************

@EndUserText.label : 'Admin Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustdrcr_admin_data {

  last_changed_by                : abp_lastchange_user;
  last_changed_at                : abp_lastchange_tstmpl;
  local_instance_last_changed_at : abp_locinst_lastchange_tstmpl;

}
********************************************************************************************************

@EndUserText.label : 'Data Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustdrcr_data {

  glaccount : hkont;
  @EndUserText.label : 'Customer'
  customer  : abap.char(10);
  dr_cr     : abap.char(10);
  amount    : abap.dec(23,2);
  debitdoc  : belnr_d;

}

******************************************************************************************************

@EndUserText.label : 'Key Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustdrcr_key {

  key client         : mandt not null;
  key uuid           : sysuuid_x16 not null;
  key companycode    : bukrs;
  key documentnumber : belnr_d;
  key postingdate    : budat;
  key postyear       : gjahr;
  key ind            : abap.char(1);

}
****************************************************************************************************

@EndUserText.label : 'Admin Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrechead_admin_data {

  created_by                     : abp_creation_user;
  created_at                     : abp_creation_tstmpl;
  last_changed_by                : abp_lastchange_user;
  last_changed_at                : abp_lastchange_tstmpl;
  local_instance_last_changed_at : abp_locinst_lastchange_tstmpl;

}
**************************************************************************************************

@EndUserText.label : 'Data Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrechead_data {

  profitcenter  : prctr;
  @EndUserText.label : 'Payer Customer Code'
  payor         : abap.char(10);
  @EndUserText.label : 'Customer Receiving the Receipt'
  customer      : abap.char(10);
  @EndUserText.label : 'Reference Number for Receipt'
  receiptnumber : abap.char(20);
  @EndUserText.label : 'Amount Received'
  receiptamount : abap.dec(15,2);
  @EndUserText.label : 'Currency'
  currency      : abap.cuky;
  @EndUserText.label : 'Document Type'
  doctype       : abap.char(2);
  @EndUserText.label : 'House bank & ID'
  housebankid   : abap.char(5);
  @EndUserText.label : 'Bank GL Account'
  bankaccount   : abap.char(10);
  @EndUserText.label : 'Remark'
  remarks       : abap.char(50);
  @EndUserText.label : 'Exchange Rate'
  exchangerate  : abap.dec(10,4);
  postingdate   : bldat;
  receiptdate   : budat;
  attachment    : zfile_content;
  mimetype      : abap.char(128);
  filename      : abap.char(128);
  status        : abap.char(1);

}
******************************************************************************************************

@EndUserText.label : 'Key Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrechead_key {

  key client     : mandt not null;
  key headeruuid : sysuuid_x16 not null;

}
*****************************************************************************************************

@EndUserText.label : 'Admin Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrecitem_admin_data {

  created_by                     : abp_creation_user;
  created_at                     : abp_creation_tstmpl;
  last_changed_by                : abp_lastchange_user;
  last_changed_at                : abp_lastchange_tstmpl;
  local_instance_last_changed_at : abp_locinst_lastchange_tstmpl;

}
*********************************************************************************************************

@EndUserText.label : 'Data Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrecitem_data {

  @EndUserText.label : 'Payment Amount'
  paymentamount             : abap.dec(23,2);
  @EndUserText.label : 'Reference Invoiceno'
  reference_invoiceno       : abap.char(16);
  @EndUserText.label : 'Amount write off'
  amountwriteoff            : abap.dec(23,2);
  @EndUserText.label : 'WO REASON CD'
  wo_reason_cd              : abap.char(2);
  @EndUserText.label : 'D/N Deduction Amount'
  deductionamount_dn        : abap.dec(23,2);
  @EndUserText.label : 'D/N REASON CD'
  reason_cd_dn              : abap.char(250);
  @EndUserText.label : 'Deduction Document Number'
  deduction_document_number : abap.char(12);
  deduction_doc_date        : datum;
  @EndUserText.label : 'TDS Receivable'
  tds_receivable            : abap.dec(23,2);
  @EndUserText.label : 'TDS Code'
  tds_code                  : abap.char(250);
  @EndUserText.label : 'Item Remarks'
  remark                    : abap.char(250);
  clearingdocument          : belnr_d;
  @EndUserText.label : 'Document Status(P/F)'
  partialfull               : abap.char(1);
  @EndUserText.label : 'Errors'
  error                     : abap.char(300);

}
****************************************************************************************************

@EndUserText.label : 'Key Fields'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
define structure zcustrecitem_key {

  key client     : mandt not null;
  key itemuuid   : sysuuid_x16 not null;
  key headeruuid : sysuuid_x16 not null;

}
********************************************************************************************************

Classes 

class ZBP_I_CUSTRECITEM_M definition
  public
  abstract
  final
  for behavior of ZI_CUSTRECITEM_M .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZBP_I_CUSTRECITEM_M IMPLEMENTATION.
ENDCLASS.
*******************************************************************************************************************

CLASS zbp_i_fi_postitems DEFINITION PUBLIC ABSTRACT FINAL FOR BEHAVIOR OF zi_postheader.
ENDCLASS.

CLASS zbp_i_fi_postitems IMPLEMENTATION.
ENDCLASS.
***************************************************************************************************

CLASS zbp_i_postheader DEFINITION PUBLIC ABSTRACT FINAL FOR BEHAVIOR OF zi_postheader.
ENDCLASS.

CLASS zbp_i_postheader IMPLEMENTATION.
ENDCLASS.
***************************************************************************************************

class ZBP_R_COSTCENTER definition
  public
  abstract
  final
  for behavior of ZI_COSTCENTER .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZBP_R_COSTCENTER IMPLEMENTATION.
ENDCLASS.
*****************************************************************************************************************

class ZBP_R_CUSTDRCR definition
  public
  abstract
  final
  for behavior of ZI_CUSTDRCR .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZBP_R_CUSTDRCR IMPLEMENTATION.
ENDCLASS.

**************************************************************************************************************************

class ZBP_R_CUSTRECHEAD definition
  public
  abstract
  final
  for behavior of ZI_CUSTRECHEAD .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZBP_R_CUSTRECHEAD IMPLEMENTATION.
ENDCLASS.

******************************************************************************************************************************************

ZFI_037_CUST_REC_CLEARING_T2 Package

Data Definitions 

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Accounting Item Details'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZI_Accountingitemdetails
  //  with parameters
  //    p_belnr : belnr_d,
  //    p_gjahr : gjahr
  as select from ZI_AcctgCustomersDetails as bsid
  //    inner join   ZI_AcctgCustomersDetails as bsid on  bsid.belnr = ZI_AcctgCustomersDetails.rebzg
  //                                                  and bsid.gjahr = ZI_AcctgCustomersDetails.rebzj
{
  // ZI_AcctgCustomersDetails.xblnr,
  bsid.rebzj,
  bsid.rebzg,

  //  bsid.xblnr,
  bsid.waers        as unit,
  @Semantics.amount.currencyCode: 'unit'
  ////////  sum( bsid.dmbtr ) as amount

  sum( bsid.wrbtr ) as amount
  // bsid.dmbtr as amount

}
//where
//      bsid.belnr = $parameters.p_belnr
//  and bsid.gjahr = $parameters.p_gjahr
group by
//  ZI_AcctgCustomersDetails.xblnr,
//  bsid.xblnr,
  bsid.rebzg,
  bsid.rebzj,
  bsid.waers
*************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'View for BSID Details'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZI_AcctgCustomersDetails as select from bsid_view
{
    key bukrs,
    key kunnr,
    key umsks,
    key umskz,
    key augdt,
    key augbl,
    key zuonr,
    key gjahr,
    key belnr,
    key buzei,
    budat,
    bldat,
    cpudt,
    waers,
    xblnr,
    blart,
    monat,
    bschl,
    zumsk,
    shkzg,
    gsber,
    tax_country,
    mwskz,
    txdat_from,
   @Semantics.amount.currencyCode: 'waers'
    dmbtr,
     @Semantics.amount.currencyCode: 'waers'
    wrbtr,
     @Semantics.amount.currencyCode: 'RFCCUR'
    fcsl,
    rfccur,
    @Semantics.amount.currencyCode: 'waers'
    mwsts,
     @Semantics.amount.currencyCode: 'waers'
    wmwst,
     @Semantics.amount.currencyCode: 'waers'
    lwsts,
     @Semantics.amount.currencyCode: 'waers'
    bdiff,
    @Semantics.amount.currencyCode: 'waers'
    bdif2,
    sgtxt,
    projn,
    aufnr,
    anln1,
    anln2,
    saknr,
    hkont,
    fkont,
    filkd,
    zfbdt,
    zterm,
    zbd1t,
    zbd2t,
    zbd3t,
    zbd1p,
    zbd2p,
     @Semantics.amount.currencyCode: 'waers'
    skfbt,
    @Semantics.amount.currencyCode: 'waers'
    sknto,
    @Semantics.amount.currencyCode: 'waers'
    wskto,
    zlsch,
    zlspr,
    zbfix,
    hbkid,
    bvtyp,
    rebzg,
    rebzj,
    rebzz,
    samnr,
    anfbn,
    anfbj,
    anfbu,
    anfae,
    mansp,
    mschl,
    madat,
    manst,
    maber,
    xnetb,
    xanet,
    xcpdd,
    xinve,
    xzahl,
    mwsk1,
    txdat_from1,
    tax_country1,
    @Semantics.amount.currencyCode: 'waers'
    dmbt1,
    @Semantics.amount.currencyCode: 'waers'
    wrbt1,
    hist_tax_factor1,
    mwsk2,
    txdat_from2,
    tax_country2,
    @Semantics.amount.currencyCode: 'waers'
    dmbt2,
    @Semantics.amount.currencyCode: 'waers'
    wrbt2,
    hist_tax_factor2,
    mwsk3,
    txdat_from3,
    tax_country3,
    @Semantics.amount.currencyCode: 'waers'
    dmbt3,
    @Semantics.amount.currencyCode: 'waers'
    wrbt3, 
    hist_tax_factor3,
    hist_tax_factor,
    bstat,
    vbund,
    vbeln,
    rebzt,
    infae,
    stceg,
    egbld,
    eglld,
    rstgr,
    xnoza,
    vertt,
    vertn,
    vbewa,
    wverw,
    projk,
    fipos,
    nplnr,
    aufpl,
    aplzl,
    xegdr,
    @Semantics.amount.currencyCode: 'waers'
    dmbe2,
    @Semantics.amount.currencyCode: 'waers'
    dmbe3,
    @Semantics.amount.currencyCode: 'waers'
    dmb21,
    @Semantics.amount.currencyCode: 'waers'
    dmb22,
    @Semantics.amount.currencyCode: 'waers'
    dmb23,
    @Semantics.amount.currencyCode: 'waers'
    dmb31,
    @Semantics.amount.currencyCode: 'waers'
    dmb32,
    @Semantics.amount.currencyCode: 'waers'
    dmb33,
    @Semantics.amount.currencyCode: 'waers'
    bdif3,
    xragl,
    uzawe,
    xstov,
    @Semantics.amount.currencyCode: 'waers'
    mwst2,
    @Semantics.amount.currencyCode: 'waers'
    mwst3,
    @Semantics.amount.currencyCode: 'waers'
    sknt2,
    @Semantics.amount.currencyCode: 'waers'
    sknt3,
    xref1,
    xref2,
    xarch,
    pswsl,
    @Semantics.amount.currencyCode: 'PSWSL'
    pswbt,
    lzbkz,
    landl,
    imkey,
    vbel2,
    vpos2,
    posn2,
    eten2,
    fistl,
    geber,
    dabrz,
    xnegp,
    kostl,
    rfzei,
    kkber,
    empfb,
    prctr,
    xref3,
    qsskz,
    zinkz,
    dtws1,
    dtws2,
    dtws3,
    dtws4,
    xpypr,
    kidno,
    @Semantics.amount.currencyCode: 'waers'
    absbt,
    ccbtc,
    pycur,
    @Semantics.amount.currencyCode: 'PYCUR'
    pyamt,
    bupla,
    secco,
    cession_kz,
    @Semantics.amount.currencyCode: 'waers'
    ppdiff,
    @Semantics.amount.currencyCode: 'waers'
    ppdif2,
    @Semantics.amount.currencyCode: 'waers'
    ppdif3,
    kblnr,
    kblpos,
    grant_nbr,
    gmvkz,
    srtype,
    lotkz,
    fkber,
    intreno,
    pprct,
    buzid,
    auggj,
    hktid,
    budget_pd,
    bdgt_account,
    re_account,
    pays_prov,
    pays_tran,
    mndid,
    payt_rsn,
    _dataaging,
    kontt,
    kontl,
    uebgdat,
    vname,
    egrup,
    btype,
    propmano,
    gkont,
    gkart,
    ghkon,
    awtyp,
    logsystem_sender,
    bukrs_sender,
    belnr_sender,
    gjahr_sender,
    buzei_sender,
    pendays,
    j_1tpbupl
}

******************************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Value help for Bank Gl Account'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
@ObjectModel.representativeKey: 'GLAccount'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true
define view entity ZI_BankGLAccountVH
  as select from I_GlAccountTextInCompanycode
{
        @ObjectModel.text.element: [ 'GLAccountName' ]
        @Search.defaultSearchElement: true
        @Search.fuzzinessThreshold: 0.8
        @Search.ranking: #HIGH
  key   GLAccount,
        @Semantics.text: true
        @Search.defaultSearchElement: true
        @Search.fuzzinessThreshold: 0.8
        @Search.ranking: #LOW
        @EndUserText.quickInfo: 'GLAccount Name'

        GLAccountName

}
where
      Language        = $session.system_language
  and CompanyCode     =       '1000'
  and ChartOfAccounts =       'RSBO'
  and GLAccount       between '0051110000' and '0051119999'
*************************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Document Type Value Help'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
//@ObjectModel.resultSet.sizeCategory: #XS
@ObjectModel.representativeKey: 'Documenttype'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true
define view entity ZI_DoctumentypeVH
  as select from t003t
{
      @ObjectModel.text.element: [ 'DocumentDescription' ]
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #HIGH
  key blart as Documenttype,
      @Semantics.text: true
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #LOW
      @EndUserText.quickInfo: 'Document Description'

      ltext as DocumentDescription
}
where
  spras = $session.system_language
*********************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Validation for Bank GL'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZI_GlValidation as select from V_T012k_DDL
{
    bukrs,
    hbkid,
    hktid,
    bankn,
    bkont,
    waers,
    refzl,
    dtaai,
    bnkn2,
    fdgrp,
    abwae,
    hkont,
    wekon,
    mindt,
    hbid1,
    hkid1,
    hbid2,
    hkid2,
    wkkon,
    wikon,
    BROLL,
    XTPRB
}
****************************************************************************************************************
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Value help for HouseBankID'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
@ObjectModel.representativeKey: 'HouseBankID'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true

define view entity ZI_HousebanckIDVh
  as select from ZI_HouseBankAccount
{
      @ObjectModel.text.element: [ 'HouseBankDescription' ]
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #HIGH
  key hbkid as HouseBankID,
      

//  key hktid,
     @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #LOW
  key hkont,
  @Semantics.text: true
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      @Search.ranking: #LOW
      @EndUserText.quickInfo: 'HouseBank Description'
      text1 as HouseBankDescription
     
}
where
      spras = $session.system_language
  and bukrs = '1000'
***************************************************************************************************************

@AbapCatalog.sqlViewName: 'ZIHOUSEBANKACC'
@AbapCatalog.compiler.compareFilter: true
@AbapCatalog.preserveKey: true
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'House Bank Account Details'
@Metadata.ignorePropagatedAnnotations: true
define view ZI_HouseBankAccount
   as select from v_t012k_bam as A
  left outer join v_t012t_bam as B on  A.bukrs = B.bukrs 
                                   and A.hbkid = B.hbkid
                                   and A.hktid = B.hktid
{
  B.spras,
  B.bukrs,
  B.hbkid,
  B.hktid,
  B.text1,
  A.hkont
}
****************************************************************************************************

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Value help for Profit center'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
@ObjectModel.dataCategory: #VALUE_HELP
@ObjectModel.representativeKey: 'ProfitCenter'
@ObjectModel.supportedCapabilities: [#SQL_DATA_SOURCE,
                                     #CDS_MODELING_DATA_SOURCE,
                                     #CDS_MODELING_ASSOCIATION_TARGET,
                                     #VALUE_HELP_PROVIDER,
                                     #SEARCHABLE_ENTITY]
@ObjectModel.modelingPattern:#NONE
@Search.searchable: true
@Consumption.ranked: true
define view entity ZI_ProfitcenterVH
  as select from I_ProfitCenterVH
{

      @ObjectModel.text.element: ['ProfitCenterName']
      @Search: {
           defaultSearchElement: true,
           ranking: #HIGH,
           fuzzinessThreshold: 0.8
          }
      @UI.textArrangement: #TEXT_LAST
  key ProfitCenter,
      @ObjectModel.text.element: ['ProfitCenterName']
      @UI.textArrangement: #TEXT_LAST
      @Search: {
           defaultSearchElement: true,
           ranking: #MEDIUM,
           fuzzinessThreshold: 0.8
          }

      ProfitCenterName,
      ProfitCtrResponsiblePersonName,
      ProfitCenterStandardHierarchy,
      Segment,
      /* Associations */
      _Segment,
      _SegmentText
}
where
  ControllingArea = 'RSBG'
  
*************************************************************************************************************************

Enhancements

ENHANCEMENT 1  .

DATA:profitcenterdata TYPE bseg-prctr.

"Update Profit Center for customer Record of Hundi Template
IMPORT profitcenter TO profitcenterdata FROM MEMORY ID 'profitcenter'.
IF profitcenterdata IS NOT INITIAL.
  LOOP AT t_bseg ASSIGNING FIELD-SYMBOL(<documentitem>) WHERE koart = 'D'.
    <documentitem>-prctr = profitcenterdata.
  ENDLOOP.
ENDIF.

******************************************************************************************************************************
*----------------------------------------------------------------------*
* BTE:            OPEN_FI_PERFORM_00001120_P                           *
*----------------------------------------------------------------------*
* Title:          Profit Center update                                 *
* RICEF#:         Profit Center update                                 *
* Transaction:                                                         *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      NTT_ABAP5 ( Vaishnavi Vairagare )                    *
* Creation Date:  10/12/2025                                           *
* Description:    Profit Center update                                 *
*----------------------------------------------------------------------*
* Modification History                                                 *
*----------------------------------------------------------------------*
* Modified by:    <Developer (full name and user name)>                *
* Date:           <Date>                                               *
* Transport:      <Transport Request #>                                *
* Description:                                                         *
*<Description of the change (or the source for the initial creation if *
* a template or SAP program was used as a starting point>              *
*----------------------------------------------------------------------*

SELECT SINGLE * FROM zfitprftcntr INTO @DATA(ls_prft_centertcode) WHERE wricefid  = 'ZPRFTCNTR'
                                                                  AND fieldname   = 'TCODE'
                                                                  AND value1      = @t_bkpf-tcode.
IF sy-subrc EQ 0.
  SELECT SINGLE * FROM zfitprftcntr INTO @DATA(ls_prft_centerdoctype) WHERE wricefid  = 'ZPRFTCNTR'
                                                                      AND fieldname   = 'DOCTYPE'
                                                                      AND value1      = @t_bkpf-blart.
  IF sy-subrc EQ 0.
    IF t_bseg[] IS NOT INITIAL.
      DATA(lt_bseg) = t_bseg[].
      DELETE lt_bseg WHERE prctr IS INITIAL.
      IF lt_bseg IS NOT INITIAL.
        READ TABLE lt_bseg INTO DATA(ls_bseg) INDEX 1.
        LOOP AT t_bseg[] ASSIGNING FIELD-SYMBOL(<fs_bseg>) WHERE prctr IS INITIAL.
          <fs_bseg>-kostl = ls_bseg-kostl.
          <fs_bseg>-prctr = ls_bseg-prctr.
        ENDLOOP.
      ENDIF.
    ENDIF.
  ENDIF.
ENDIF.

"" End of changes 10.12.2025 by Vaishnavi Vairagare
******************************************************************************************************************************
ENDENHANCEMENT.

************************************************************************************************************************************************

Classes 

CLASS zbp_i_postnclear_documents_m DEFINITION PUBLIC ABSTRACT FINAL FOR BEHAVIOR OF zi_postnclear_documents_m.
ENDCLASS.

CLASS zbp_i_postnclear_documents_m IMPLEMENTATION.
ENDCLASS.
****************************************************************************************************************************

class ZPOSTANDCLEARDOCUMENT definition
  public
  final
  create public .

public section.

  types:
    BEGIN OF result,
        errormsg TYPE c LENGTH 256,
        flag     TYPE char1,
        document TYPE belnr_d,
        year     TYPE gjahr,
      END OF result .
  types:
    BEGIN OF docclearingdata,
        documentdetails        TYPE STANDARD TABLE OF blntab WITH EMPTY KEY,
        clearingdetails        TYPE STANDARD TABLE OF ftclear WITH EMPTY KEY,
        doc_header_itemdetails TYPE STANDARD TABLE OF ftpost WITH EMPTY KEY,
        taxes                  TYPE STANDARD TABLE OF fttax WITH EMPTY KEY,
      END OF docclearingdata .
  types:
    BEGIN OF docpartialclearingdata,
        doc_header_itemdetails TYPE STANDARD TABLE OF bdcdata WITH EMPTY KEY,
      END OF docpartialclearingdata .

  class-data MSGID type SY-MSGID .
  class-data MSGNO type SY-MSGNO .
  class-data MSGTY type SY-MSGTY .
  class-data MSGV1 type SY-MSGV1 .
  class-data MSGV2 type SY-MSGV2 .
  class-data MSGV3 type SY-MSGV3 .
  class-data MSGV4 type SY-MSGV4 .
  class-data SUBRC type SY-SUBRC .
  class-data AUGLV type T041A-AUGLV value 'EINGZAHL' ##NO_TEXT.
  class-data TCODE type SY-TCODE value 'FB05' ##NO_TEXT.
  class-data SGFUNCT type RFIPI-SGFUNCT value 'C' ##NO_TEXT.

  class-methods FULLPOSTANDCLEAR
    importing
      value(CLEARINGDETAILS) type DOCCLEARINGDATA optional
    returning
      value(RESULT) type RESULT .
  class-methods PARTIALCLEAR
    importing
      value(CLEARINGDETAILS) type DOCPARTIALCLEARINGDATA optional
    returning
      value(RESULT) type RESULT .
  class-methods HUNDIPARTIAL
    importing
      value(PROFITCENTER) type BSEG-PRCTR optional
      value(CLEARINGDETAILS) type DOCPARTIALCLEARINGDATA optional
    returning
      value(RESULT) type RESULT .
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.



CLASS ZPOSTANDCLEARDOCUMENT IMPLEMENTATION.


  METHOD fullpostandclear.

    DATA : belnr TYPE    belnr_d,
           bukrs TYPE    bukrs,
           gjahr TYPE    gjahr,
           error TYPE string.

    CALL FUNCTION 'Z_POSTINGWITHCLEARING' DESTINATION 'NONE'
      IMPORTING
        msgid                  = msgid
        msgno                  = msgno
        msgty                  = msgty
        msgv1                  = msgv1
        msgv2                  = msgv2
        msgv3                  = msgv3
        msgv4                  = msgv4
        subrc                  = subrc
        belnr                  = belnr
        bukrs                  = bukrs
        gjahr                  = gjahr
        error                  = error
      TABLES
        documentdetails        = clearingdetails-documentdetails[]
        clearingdetails        = clearingdetails-clearingdetails[]
        doc_header_itemdetails = clearingdetails-doc_header_itemdetails[]
        taxes                  = clearingdetails-taxes[].

    IF belnr IS NOT INITIAL ."AND  bukrs IS NOT INITIAL AND gjahr IS NOT INITIAL.
      result = VALUE #( flag     = /isdfps/cl_const_abc_123=>gc_s
                        document = belnr
                        year     = gjahr
                        errormsg = error ).
    ELSE.
      result = VALUE #( flag     = /isdfps/cl_const_abc_123=>gc_e
                        errormsg = error ).
    ENDIF.

    CLEAR:clearingdetails-documentdetails[],clearingdetails-clearingdetails[],
    clearingdetails-doc_header_itemdetails[],clearingdetails-taxes[].

  ENDMETHOD.


  METHOD partialclear.

    DATA:"bdcdata TYPE TABLE OF bdcdata,
      messtab  TYPE TABLE OF bdcmsgcoll,
      error    TYPE string,
      document TYPE belnr_d.

    CALL FUNCTION 'Z_POSTINGPARTIAL_PAYMENT' DESTINATION 'NONE'
      IMPORTING
        error           = error
        document        = document
      TABLES
        clearingdetails = clearingdetails-doc_header_itemdetails[]
        messtab         = messtab.

    result = VALUE #( errormsg = error
                      document = document
                      flag     = COND #( WHEN VALUE #( messtab[ msgnr = '312' ]-msgv1 OPTIONAL ) IS INITIAL
                      THEN 'E'
                      ELSE 'S' ) ).

    CLEAR :clearingdetails-doc_header_itemdetails[],messtab[].
  ENDMETHOD.


  METHOD hundipartial.
    DATA: messtab  TYPE TABLE OF bdcmsgcoll,
          error    TYPE string,
          document TYPE belnr_d.

    CALL FUNCTION 'Z_POSTINGPARTIAL_HUNDI_PAYMENT' DESTINATION 'NONE'
      EXPORTING
        profitcenter    = profitcenter
      IMPORTING
        error           = error
        document        = document
      TABLES
        clearingdetails = clearingdetails-doc_header_itemdetails[]
        messtab         = messtab.

*    CALL FUNCTION 'Z_POSTINGPARTIAL_HUNDI_PAYMENT' DESTINATION 'NONE'
*      IMPORTING
*        error           = error
*        document        = document
*      TABLES
*        clearingdetails = clearingdetails-doc_header_itemdetails[]
*        messtab         = messtab.

    result = VALUE #( errormsg = error
                      document = document
                      flag     = COND #( WHEN VALUE #( messtab[ msgnr = '312' ]-msgv1 OPTIONAL ) IS INITIAL
                      THEN 'E'
                      ELSE 'S' ) ).

    CLEAR :clearingdetails-doc_header_itemdetails[],messtab[].
  ENDMETHOD.
ENDCLASS.
*****************************************************************************************************************************






