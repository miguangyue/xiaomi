angular前端excel导出解决文件过大问题处理：
exportList(obj) {
  let obj = {
            html: '<tr style='background:#DDECE9;font-weight:bold;'><td >序号</td><td style=vnd.ms-excel.numberformat:@>xxx</td></tr>...',
            name: '订单列表',
  };
 let   template =
 '<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:x="urn:schemas-microsoft-com:office:excel" xmlns="http://www.w3.org/TR/REC-html40"><head><!--[if gte mso 9]><xml><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet><x:Name>{worksheet}</x:Name><x:WorksheetOptions><x:DisplayGridlines/></x:WorksheetOptions></x:ExcelWorksheet></x:ExcelWorksheets></x:ExcelWorkbook></xml><![endif]--></head><body><table border=1 width=1002 style="text-align:center">{table}</table></body></html>';
        let ctx = {
            worksheet: name || 'Worksheet',
            table: obj.html
        };
        const data: Blob = new Blob([this.format(template, ctx)], {
            type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=UTF-8'
        });
        FileSaver.saveAs(data, `${obj.name}${moment().format('YYYY-MM-DD')}.xls`);
    }
    
    format (s, c) {
        return s.replace(/{(\w+)}/g, function (m, p) {
            return c[p];
        });
    }
