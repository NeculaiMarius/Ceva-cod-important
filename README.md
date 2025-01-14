# Ceva-cod-important

package ro.ase.ie.g1096_s05.networking;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Handler;
import android.os.Looper;
import android.util.Log;
import android.widget.ImageView;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.security.cert.X509Certificate;

import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;

public class DownloadJSONTask implements Runnable {
    private final String movieUrl;
    private static String jsonContent;

    public DownloadJSONTask(String movieUrl) {
        this.movieUrl = movieUrl;
    }

    @Override
    public void run() {
        try {
            disableSSLCertificateChecking();
            
            URL url = new URL(this.movieUrl);
            URLConnection urlConnection = url.openConnection();
            urlConnection.connect();
            InputStream inputStream = urlConnection.getInputStream();
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));


            StringBuilder result = new StringBuilder();
            // Ii lipsea tampenia asta de paranteza si crapa. Nu prea se face asa, dar ayaye
            result.append("[");
            for(String line; (line = bufferedReader.readLine())!= null;) {
                if(result.length() > 0) {
                    result.append("\n");
                }
                result.append(line);
            }

            this.jsonContent = result.toString();

//
//            new Handler(Looper.getMainLooper()).post(new Runnable() {
//                @Override
//                public void run() {
//                    ivPoster.setImageBitmap(bitmap);
//                }
//            });


        } catch (MalformedURLException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private void disableSSLCertificateChecking() {
        try {
            TrustManager[] trustAllCerts = new TrustManager[]{
                    new X509TrustManager() {
                        @Override
                        public void checkClientTrusted(X509Certificate[] chain, String authType) {}

                        @Override
                        public void checkServerTrusted(X509Certificate[] chain, String authType) {}

                        @Override
                        public X509Certificate[] getAcceptedIssuers() {
                            return new X509Certificate[0];
                        }
                    }
            };

            SSLContext sc = SSLContext.getInstance("TLS");
            sc.init(null, trustAllCerts, new java.security.SecureRandom());
            HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());

            HttpsURLConnection.setDefaultHostnameVerifier((hostname, session) -> true);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static String getJsonContent() {
        return jsonContent;










ALA DIN MAIN


databaseManager = DatabaseManager.getInstance(this);
        movieDao = databaseManager.getMovieReference();

//        String jsonFromResources = JsonUtil.getJsonFromResources(this, R.raw.movies);
//        ArrayList<Movie> movies = JsonUtil.parseJsonContent(jsonFromResources);
//        movieArrayList.addAll(movies);

        DownloadJSONTask downloadJSONTask = new DownloadJSONTask("https://jsonkeeper.com/b/6O40");
        Thread threadJSON = new Thread(downloadJSONTask);
        threadJSON.start();

        try {
            threadJSON.join(); // Wait for the thread to finish
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        String jsonContent = DownloadJSONTask.getJsonContent();

        Log.d("IMPORTANT", jsonContent);
        ArrayList<Movie> movies = JsonUtil.parseJsonContent(jsonContent);
        movieArrayList.addAll(movies);
    }

    public static void setJsonContent(String jsonContent) {
        jsonContent = jsonContent;
    }
}
