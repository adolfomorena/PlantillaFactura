<t t-name="account.report_invoice_document">
            <t t-call="web.external_layout">
                <t t-set="o" t-value="o.with_context(lang=lang)"/>
                <div class="page">
                    <div class="row">
                        <div class="col">
                            <strong>Factura</strong>
                            <span t-if="o.name == '/'" t-field="o.invoice_origin"/>
                            <span t-else="" t-field="o.name"/>
                            <span t-if="o.name == '/'">Pendiente de pago</span>
                            <span t-else="">Pagada</span>
                        </div>
                    </div>
                    <div class="row mt-5">
                        <div class="col">
                            <strong>Nombre:</strong> <span t-field="o.partner_id.name"/>
                        </div>
                        <div class="col">
                            <strong>Direccion:</strong> <span t-field="o.partner_id.city"/>
                        </div>
                        <div class="col">
                            <strong>Correo:</strong> <span t-field="o.partner_id.email"/>
                        </div>
                    </div>
                    <t t-set="lines" t-value="o.invoice_line_ids.sorted(key=lambda l: (-l.sequence, l.date, l.move_name, -l.id), reverse=True)"/>
                        <t t-set="current_subtotal" t-value="0"/>
                        <t t-set="current_total" t-value="0"/>
                    <t t-foreach="lines" t-as="line">
                        <t t-set="current_subtotal" t-value="current_subtotal + line.price_subtotal"/>
                        <t t-set="current_total" t-value="current_total + line.price_total"/>
                    </t>
                    <div class="oe-structure mt-5">
                        <table class="table">
                            <thead class="text-center">
                                <tr>
                                    <th>Nombre</th>
                                    <th>Unidades</th>
                                    <th>Tasas</th>
                                    <th>Precio Unidad</th>
                                    <th>Precio Tasas</th>
                                    <th>Total con tasas</th>
                                </tr>
                            </thead>
                            <tbody class="text-center">
                                 <t t-foreach="o.invoice_line_ids" t-as="line">
                                    <t t-set="total_tasas" t-value="0"/>
                                    <t t-set="total_productos" t-value="0"/>
                                    <tr>
                                        <td><span t-field="line.product_id.name"/></td>
                                        <td><span t-field="line.quantity"/></td>
                                        <td>
                                            <t t-foreach="line.tax_ids" t-as="tax">
                                            <span t-field="tax.name"/><br/>
                                            </t>
                                        </td>
                                        <td><span t-field="line.price_unit"/></td>
                                        <td>
                                            <t t-set="total_sin_impuestos" t-value="line.price_unit * line.quantity"/>
                                            <t t-set="total_porcentaje_tasas" t-value="sum(tax.amount for tax in line.tax_ids)"/>
                                            <t t-set="importe_tasa" t-value="total_sin_impuestos * (total_porcentaje_tasas / 100)"/>
                                            <span t-esc="importe_tasa"></span>
                                            <t t-set="total_tasas" t-value="total_tasas + importe_tasa"/>
                                        </td>
                                        <td><span t-field="line.price_total"/></td>
                                        <t t-set="total_productos" t-value="total_productos + line.quantity"/>
                                    </tr>
                                </t>
                            </tbody>
                        </table>
                    </div>
                    <div class="row mt-5">
                        <div class="col">
                            <strong colspan="4">Total de Tasas: </strong><span t-esc="total_tasas"/>
                        </div>
                        <div class="col">
                            <strong colspan="4">Numero Total de Productos: </strong><span t-esc="total_productos"/>
                        </div>   
                    </div>
                </div>
            </t>
        </t>
