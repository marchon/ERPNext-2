# ERPNext
Custom scripts for ERPNext
//Updating custom fields for complex calculations as the user inputs/edits data:
//Uses a function to update all fields that are affected by a change to a specific field.

cur_frm.cscript.buying_price 
= function() {
cur_frm.set_value("vat_on_uplifted", "buying_price" );
cur_frm.set_value("duty", "buying_price" );
cur_frm.set_value("rate_before_shipping", "buying_price" );
cur_frm.set_value("landed_cost", "buying_price" );
cur_frm.set_value("mark_up", "buying_price" );
cur_frm.set_value("vat_gap", "buying_price" );
cur_frm.set_value("rounded_retail_price", "buying_price" );
cur_frm.set_value("retail_ex_vat", "buying_price" );
cur_frm.set_value("profit", "buying_price" );
cur_frm.set_value("trade_price", "buying_price" );
}

cur_frm.cscript.duty_percent 
= function() {
cur_frm.set_value("duty", "duty_percent" );
cur_frm.set_value("rate_before_shipping", "duty_percent" );
cur_frm.set_value("landed_cost", "duty_percent" );
cur_frm.set_value("mark_up", "duty_percent" );
cur_frm.set_value("vat_gap", "duty_percent" );
cur_frm.set_value("rounded_retail_price", "duty_percent" );
cur_frm.set_value("retail_ex_vat", "duty_percent" );
cur_frm.set_value("profit", "duty_percent" );
cur_frm.set_value("trade_price", "duty_percent" );
}

cur_frm.cscript.current_exchange 
= function() {
cur_frm.set_value("rate_before_shipping", "current_exchange" );
cur_frm.set_value("landed_cost", "current_exchange" );
cur_frm.set_value("mark_up", "current_exchange" );
cur_frm.set_value("vat_gap", "current_exchange" );
cur_frm.set_value("rounded_retail_price", "current_exchange" );
cur_frm.set_value("retail_ex_vat", "current_exchange" );
cur_frm.set_value("profit", "current_exchange" );
cur_frm.set_value("trade_price", "current_exchange" );
}

cur_frm.cscript.shipping_per_cbm 
= function() {
cur_frm.set_value("freight_cost", "shipping_per_cbm" );
cur_frm.set_value("landed_cost", "shipping_per_cbm" );
cur_frm.set_value("mark_up", "shipping_per_cbm" );
cur_frm.set_value("vat_gap", "shipping_per_cbm" );
cur_frm.set_value("rounded_retail_price", "shipping_per_cbm" );
cur_frm.set_value("retail_ex_vat", "shipping_per_cbm" );
cur_frm.set_value("profit", "shipping_per_cbm" );
cur_frm.set_value("trade_price", "shipping_per_cbm" );
}

cur_frm.cscript.freight_cbm 
= function() {
cur_frm.set_value("freight_cost", "freight_cbm" );
cur_frm.set_value("landed_cost", "mark_up_percent" );
cur_frm.set_value("mark_up", "mark_up_percent" );
cur_frm.set_value("vat_gap", "mark_up_percent" );
cur_frm.set_value("rounded_retail_price", "freight_cbm" );
cur_frm.set_value("retail_ex_vat", "freight_cbm" );
cur_frm.set_value("profit", "mark_up_percent" );
cur_frm.set_value("trade_price", "mark_up_percent" );
}

cur_frm.cscript.mark_up_percent
= function() {
cur_frm.set_value("mark_up", "mark_up_percent" );
cur_frm.set_value("vat_gap", "mark_up_percent" );
cur_frm.set_value("rounded_retail_price", "mark_up_percent" );
cur_frm.set_value("retail_ex_vat", "mark_up_percent" );
cur_frm.set_value("profit", "mark_up_percent" );
cur_frm.set_value("trade_price", "mark_up_percent" );
}

cur_frm.cscript.commissions_percentage
= function() {
cur_frm.set_value("profit", "commissions_percentage" );
}

frappe.ui.form.on("Item", "vat_on_uplifted", function(frm) {
  frm.set_value("vat_on_uplifted", (frm.doc.buying_price * 1.15) * 0.14);
});

frappe.ui.form.on("Item", "duty", function(frm) {
  frm.set_value("duty", (frm.doc.buying_price * 1.15) * frm.doc.duty_percent/100);
});

frappe.ui.form.on("Item", "rate_before_shipping", function(frm) {
  frm.set_value("rate_before_shipping", (frm.doc.buying_price + frm.doc.vat_on_uplifted + frm.doc.duty) * frm.doc.current_exchange );
});

frappe.ui.form.on("Item", "freight_cost", function(frm) {
  frm.set_value("freight_cost", frm.doc.freight_cbm * frm.doc.shipping_per_cbm);
});

frappe.ui.form.on("Item", "landed_cost", function(frm) {
  frm.set_value("landed_cost", frm.doc.rate_before_shipping + frm.doc.freight_cost);
});

frappe.ui.form.on("Item", "mark_up", function(frm) {
  frm.set_value("mark_up", frm.doc.landed_cost * frm.doc.mark_up_percent/100);
});

frappe.ui.form.on("Item", "vat_gap", function(frm) {
  frm.set_value("vat_gap", frm.doc.mark_up * 0.16);
});

frappe.ui.form.on("Item", "rounded_retail_price", function(frm) {
var num = frm.doc.landed_cost + frm.doc.mark_up + frm.doc.vat_gap;
  frm.set_value("rounded_retail_price", Math.ceil(num / 100) * 100);
});

frappe.ui.form.on("Item", "trade_price", function(frm) {
  frm.set_value("trade_price", frm.doc.rounded_retail_price * 0.85);
});

frappe.ui.form.on("Item", "retail_ex_vat", function(frm) {
  frm.set_value("retail_ex_vat", frm.doc.rounded_retail_price / 1.14);
});

frappe.ui.form.on("Item", "profit", function(frm) {
  frm.set_value("profit", frm.doc.retail_ex_vat - (frm.doc.rounded_retail_price * frm.doc.commissions_percentage/100) - frm.doc.landed_cost);
});

frappe.ui.form.on("Item", "trade_price", function(frm) {
  frm.set_value("trade_price", (frm.doc.rounded_retail_price * 0.85));
});
