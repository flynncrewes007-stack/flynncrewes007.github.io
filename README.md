Flynn Crewes Dashboard
import os
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib import colors

def create_invoice(invoice_number, client_name, items, file_name="invoice.pdf"):
    # Setup document
    doc = SimpleDocTemplate(file_name, pagesize=letter,
                            rightMargin=40, leftMargin=40,
                            topMargin=40, bottomMargin=40)
    
    # Setup styles
    styles = getSampleStyleSheet()
    title_style = ParagraphStyle(name='Title', parent=styles['Heading1'], fontSize=24, leading=28, textColor=colors.HexColor('#2C3E50'), alignment=2)
    sub_title = ParagraphStyle(name='SubTitle', parent=styles['Normal'], fontSize=10, leading=14, textColor=colors.HexColor('#7F8C8D'), alignment=2)
    h2_style = ParagraphStyle(name='Heading2', parent=styles['Heading2'], fontSize=12, leading=16, textColor=colors.HexColor('#34495E'))
    normal_style = ParagraphStyle(name='Normal', parent=styles['Normal'], fontSize=10, leading=14, textColor=colors.HexColor('#333333'))

    elements = []
    
    # 1. Header
    header_data = [
        [Paragraph("<b>INVOICE</b>", title_style), Paragraph("<b>Your Company Name</b>", sub_title)],
        ["", Paragraph("123 Business Rd, Sydney, NSW", sub_title)],
        ["", Paragraph("contact@yourcompany.com", sub_title)],
    ]
    header_table = Table(header_data, colWidths=[300, 240])
    header_table.setStyle(TableStyle([('VALIGN', (0,0), (-1,-1), 'TOP')]))
    elements.append(header_table)
    elements.append(Spacer(1, 20))

    # 2. Bill To
    elements.append(Paragraph("<b>BILL TO:</b>", h2_style))
    elements.append(Paragraph(client_name, normal_style))
    elements.append(Spacer(1, 20))
    
    # 3. Invoice Details Table
    table_data = [["Description", "Qty", "Rate", "Amount"]]
    subtotal = 0
    
    for desc, qty, rate in items:
        amount = qty * rate
        subtotal += amount
        table_data.append([desc, str(qty), f"${rate:.2f}", f"${amount:.2f}"])
        
    tax = subtotal * 0.10  # 10% GST
    total = subtotal + tax
    
    table_data.append(["", "", "Subtotal:", f"${subtotal:.2f}"])
    table_data.append(["", "", "GST (10%):", f"${tax:.2f}"])
    table_data.append(["", "", "Total:", f"${total:.2f}"])
    
    # Table Styling
    invoice_table = Table(table_data, colWidths=[300, 60, 90, 90])
    invoice_table.setStyle(TableStyle([
        ('BACKGROUND', (0,0), (-1,0), colors.HexColor('#2C3E50')),
        ('TEXTCOLOR', (0,0), (-1,0), colors.white),
        ('ALIGN', (0,0), (-1,-1), 'LEFT'),
        ('ALIGN', (1,0), (-1,-1), 'RIGHT'),
        ('FONTNAME', (0,0), (-1,0), 'Helvetica-Bold'),
        ('BOTTOMPADDING', (0,0), (-1,0), 8),
        ('GRID', (0,0), (-1,-5), 0.5, colors.HexColor('#BDC3C7')),
        ('LINEABOVE', (-3,-3), (-1,-1), 1, colors.HexColor('#2C3E50')),
        ('FONTNAME', (-2,-1), (-1,-1), 'Helvetica-Bold'),
    ]))
    
    elements.append(invoice_table)
    doc.build(elements)
    print(f"Invoice saved as {file_name}")
