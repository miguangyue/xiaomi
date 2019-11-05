angular前端excel导出解决文件过大问题处理：
 格式： obj = {
            html: '<tr style='background:#DDECE9;font-weight:bold;'><td >序号</td><td style=vnd.ms-excel.numberformat:@>xxx</td></tr>...',
            name: '订单列表',
  };
  方式1：
exportList(obj) {
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
    方式2：
     exportList(obj) {
        const worksheet: XLSX.WorkSheet = XLSX.utils.json_to_sheet(obj.jsonData);
        worksheet['!cols'] = obj.cols;
        const workbook: XLSX.WorkBook = { Sheets: { 'data': worksheet }, SheetNames: ['data'] };
        const excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' });
        const data: Blob = new Blob([excelBuffer], {
            type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=UTF-8'
        });
        FileSaver.saveAs(data, `${obj.name}${moment().format('YYYY-MM-DD')}.xls`);
    }
    方式3：
    exportExcel(obj) {
            let str = obj.tableTitle, uri;
            // tableTitle = `姓名,电话,邮箱\n`;
            if (obj.jsonData.length) {
                obj.jsonData.map(jsonItem => {
                    let keys = Object.keys(jsonItem);
                    if (keys.length) {
                        keys.map(keyItem => {
                            str += `${jsonItem[keyItem] + '\t'},`;
                        });
                        str += '\n';
                    }
                });
            }
            uri = 'data:text/csv;charset=utf-8,\ufeff' + encodeURIComponent(str);
            // 通过创建a标签实现
            let link = document.createElement("a");
            link.href = uri;
            // 对下载的文件命名
            link.download = `${obj.name}.xls`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }
