        $.ajax({
					url : "<c:url value='fileboardList.do'/>",
					success : function(data) {

						var html = "";
						for (key in data) {
							html += '<tr>';
							html += '<td>' + data[key].seq + '</td>';
							html += '<td>' + data[key].title + '</td>';
							html += '<td>' + data[key].regId + '</td>';
							if (data[key].fileId !== 0) {
								html += '<td><i class="bi bi-file-earmark-arrow-down"></i></td>';
							} else {
								html += '<td></td>';
							}

							html += '<td>' + datereplace(data[key].regDt)
									+ '</td>';
							html += '</tr>';
						}
						$("#dynamicTbody").empty();
						$("#dynamicTbody").append(html);
					}
				})