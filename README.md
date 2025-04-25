# Silver-Termometer-Cam-
Cam Wyze v3 con AI para estimar temperatura de plata fundida.
    
    
    import cv2
    import numpy as np
    
    # Cargar imagen desde la cámara (adaptarlo para captura en tiempo real)
    image = cv2.imread("metal_fundido.jpg")
    
    # Convertir la imagen a espacio de color HSV
    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    
    # Definir rangos de color rojo en HSV
    red_lower1 = np.array([0, 120, 70])
    red_upper1 = np.array([10, 255, 255])
    red_lower2 = np.array([170, 120, 70])
    red_upper2 = np.array([180, 255, 255])
    
    # Crear máscara para detectar rojo
    red_mask1 = cv2.inRange(hsv, red_lower1, red_upper1)
    red_mask2 = cv2.inRange(hsv, red_lower2, red_upper2)
    red_mask = red_mask1 + red_mask2
    
    # Extraer solo los píxeles rojos de la imagen original
    red_pixels = cv2.bitwise_and(image, image, mask=red_mask)
    
    # Convertir la imagen filtrada a escala de grises para analizar intensidad
    gray_red = cv2.cvtColor(red_pixels, cv2.COLOR_BGR2GRAY)
    
    # Calcular la intensidad promedio del rojo en la imagen
    red_intensity = np.mean(gray_red[gray_red > 0])  # Evita contar valores de fondo (cero)
    
    # Estimar temperatura basada en la intensidad del rojo
    if red_intensity >= 200:
        estimated_temp = "Alta temperatura (>1000°C)"
    elif 150 <= red_intensity < 200:
        estimated_temp = "Temperatura moderada (800-1000°C)"
    elif 100 <= red_intensity < 150:
        estimated_temp = "Temperatura baja (600-800°C)"
    else:
        estimated_temp = "No se detecta suficiente rojo para estimar temperatura"
    
    print(f"Estimación de temperatura: {estimated_temp}")
    
    # Mostrar la imagen con detección de rojo
    cv2.imshow("Detección de Rojo", red_pixels)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
