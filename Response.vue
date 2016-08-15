<template>
    <div class="alerts-box">
        <div v-for="alert in alerts" class="alert alert-{{ alert.type }}">{{ alert.message }}</div>
    </div>
</template>

<script>
    export default {
        props: ['passed_alerts'],
        data() {
            return {
                alerts: []
            }
        },
        ready(){
            if(this.passed_alerts){
                this.addAlert(this.passed_alerts, "success", 6000)
            }
            this.alerts = this.spark.alerts;
        },
        mixins: {
            methods: {
                processResponse(response, notify){
                    var alert_type = response.status == "success" ? "success" : "danger";
                    var alert = (typeof response.message !== "undefined" && response.message != "") ? this.getResponseMessage(response) : '';
                    var redirect = (typeof response.redirect !== "undefined" && response.redirect != "") ? response.redirect : null;

                    if( alert != ""){
                        if(notify){
                            alert_type = alert_type == "success" ? alert_type : "error"
                            this.finalAlert(this.getResponseMessage(response), this.getResponseSubMessage(response), alert_type, redirect)
                        } else {
                            if(redirect){
                                window.location = response.redirect + "?alert=" + alert
                            } else {
                                this.addAlert(alert, alert_type);
                            }
                        }
                    } else {
                        if(redirect){
                            window.location = response.redirect
                        }
                    }
                },
                addAlert(message, type, fadeTime){
                    fadeTime = typeof fadeTime !== 'undefined' ? fadeTime : 3000;
                    var alert = Spark.alerts.push({
                        message: message,
                        type: type
                    });
                    setTimeout(function(){
                        Spark.alerts.splice(alert-1,1);
                    }, fadeTime);
                },
                responseAlert(response){
                    var alert = Spark.alerts.push({
                        message: response.message,
                        type: response.status == "success" ? "success" : "danger"
                    });
                    setTimeout(function(){
                        Spark.alerts.splice(alert-1,1);
                    }, 3000);
                },
                objectToArray(obj){
                    var _Array = new Array();
                    for(var name in obj){
                        _Array[name] = obj[name];
                    }
                    return _Array;
                },
                getResponseData(response, toArray){
                    toArray = typeof toArray !== 'undefined' ? toArray : false;
                    var return_data = []
                    if(typeof response.data !== "undefined"){
                        if(typeof response.data.data !== "undefined"){
                            return_data = response.data.data
                        } else {
                            return_data = response.data
                        }
                    }

                    return_data = this.objectToArray(return_data)

                    return return_data
                },
                getResponseData(response, toArray){
//                    toArray = typeof toArray !== 'undefined' ? toArray : false;
                    var return_data = []
                    if(typeof response.data !== "undefined"){
                        if(typeof response.data.data !== "undefined"){
                            return_data = response.data.data
                        } else {
                            return_data = response.data
                        }
                    }

                    return_data = this.objectToArray(return_data)

                    return return_data
                },
                getResponsePagination(response){
                    var return_data = {}
                    if(typeof response.data !== "undefined"){
                        if(typeof response.data.total !== "undefined"){
                            return_data = {
                                total: parseInt(response.data.total),
                                per_page: parseInt(response.data.per_page),
                                current_page: parseInt(response.data.current_page)
                            }
                        } else {
                            return_data = {
                                total: parseInt(response.total),
                                per_page: parseInt(response.per_page),
                                current_page: parseInt(response.current_page)
                            }
                        }
                    }

                    return return_data
                },
                getResponseMessage(response){
                    if(typeof response.data !== "undefined"){
                        if(typeof response.data.message !== "undefined"){
                            return response.data.message
                        }
                    }
                    if(typeof response.message !== "undefined"){
                        return response.message
                    }
                    return null
                },
                getResponseSubMessage(response){
                    if(typeof response.data !== "undefined"){
                        if(typeof response.data.submessage !== "undefined"){
                            return response.data.submessage
                        }
                    }
                    if(typeof response.submessage !== "undefined"){
                        return response.submessage
                    }
                    return null
                },
                sweetConfirm(callback){
                    $('.modal').modal('hide');
                    swal({
                        title: "Are you sure?",
                        type: "warning",
                        showCancelButton: true,
                        confirmButtonColor: "#DD6B55",
                        confirmButtonText: "Yes",
                        cancelButtonText: "Cancel",
                        closeOnConfirm: true,
                        closeOnCancel: true
                    }, function (isConfirm) {
                        if (isConfirm) {
                            if(typeof callback === "function"){
                                callback()
                            }
                        }
                    });
                },
                sweetAlert(title, text, type, redirect){
                    $('.modal').modal('hide');
                    redirect = typeof redirect !== 'undefined' ? redirect : false;
                    type = typeof type !== 'undefined' ? type : "info";
                    text = typeof text !== 'undefined' ? text : "";
                    title = typeof title !== 'undefined' ? title : "Success";
                    swal({
                        title: title,
                        text: text,
                        type: type,
                        showCancelButton: false,
                        confirmButtonColor: "#DD6B55",
                        confirmButtonText: "Ok",
                        closeOnConfirm: true
                    }, function (isConfirm) {
                        if (isConfirm && redirect) {
                            window.location = redirect;
                        } else {
                            return false
                        }
                    });
                }
            }
        }
    }
</script>

<style>
    .alerts-box{
        position: fixed;
        top: 100px;
        right: 15px;
        max-width: 300px;
        z-index: 9999;
    }
</style>