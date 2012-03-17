meus_andamentos += '<div id="andamento_processual_index"><table class="table_index"><thead><tr>'
        meus_andamentos += '<th class="th_index">Processo</th><th class="th_index">Tipo do andamento</th>'
        meus_andamentos += '<th class="th_index">Ata</th><th class="th_index">Data</th></tr></thead>'
        meus_andamentos += '<tbody>'
        usuario = get_usuario(request, request.user.email)
        andamentos = Andamento.objects.filter(Q(fkProcesso__fkFuncionario = usuario.pk) | Q(fkProcesso__fkCadastrador = usuario.pk)).filter(fkProcesso__ativo=True).distinct()
        lista_email_cliente_andamento = andamentos.values_list('fkProcesso__clienteProcesso__nome','fkProcesso__clienteProcesso__email')
        
        #Carregando os e-mails dos clientes pertencentes ao andamento/processo
        lista_email_cliente = []        
        for cliente in lista_email_cliente_andamento:
            lista_email_cliente.append(str(cliente[0])+'#'+str(cliente[1]))
                    
        for andamento in andamentos:
            dado_andamento = '%s#%s#%s#%s' %(str(andamento.pk), str(andamento.fkProcesso) ,str(andamento.data.strftime("%d/%m/%Y")) ,unicode(andamento.ata))            
            meus_andamentos += '<tr><td><a href="#" id="andamento_%s">%s</a></td><td>%s</td><td>%s...</td><td>%s</td></tr>' %(str(andamento.pk), str(andamento.fkProcesso), str(andamento.fkTipoAndamento), unicode(andamento.ata)[:5], str(andamento.data.strftime("%d/%m/%Y")))
            div_andamentos += '<div id="div_dialog_detalhe_andamento_%s" title="andamento para o prazo: %s">Tipo do andamento: %s<br />Data: %s<br />Ata:<br />%s<br /><br /><br /><a href="Processo/andamento/%s" class="link-button link-button-submit">Editar</a>&nbsp;&nbsp;&nbsp;<a href="#" id="link_div_filha_modal_email_andamento" class="link-button link-button-submit">Enviar e-mail</a>' %(str(andamento.pk), str(andamento.fkProcesso), unicode(andamento.fkTipoAndamento), str(andamento.data.strftime("%d/%m/%Y")), unicode(andamento.ata), int(andamento.pk))
            div_andamentos += '<div id="div_filha_modal_envio_email_andamento" class="div_filha_modal"><form id="formulario_envio_andamento" name="formulario_envio_andamento" method="POST"><input id="hidden_emails_andamento" name="hidden_emails_andamento" type="hidden" value="%s" /><input id="hidden_dados_andamento" name="hidden_dados_andamento" type="hidden" value="%s" /><textarea id="mensagem_envio_andamento" name="mensagem_envio_andamento" rows="3" cols="50" placeholder="Mensagem a ser enviado junto com o andamento."></textarea><br /><br /><a href=# id="enviar_andamento_cliente" class="link-button link-button-red">Enviar andamento para cliente(s)</a></form></div>' %(str(lista_email_cliente), unicode(dado_andamento))
            div_andamentos += '<div id="div_resposta_envio_andamento">Enviando e-mail.</div>'
            div_andamentos += '</div>'
        meus_andamentos += div_andamentos
        meus_andamentos += '</tbody><tfoot><tr><td colspan="4">Quantidade de andamentos %s</td></tr></tfoot></table></div>' %(str(andamentos.count()))        
    